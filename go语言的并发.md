# go语言的并发

## 一. 概述

Go语言的并发通过`goroutine`实现。`goroutine`类似于线程，属于用户态的线程，我们可以根据需要创建成千上万个`goroutine`并发工作。`goroutine`是由Go语言的运行时（runtime）调度完成，而线程是由操作系统调度完成。

Go语言还提供`channel`在多个`goroutine`间进行通信。`goroutine`和`channel`是 Go 语言秉承的 CSP（Communicating Sequential Process）并发模式的重要实现基础。

#### goroutine

在java/c++中我们要实现并发编程的时候，我们通常需要自己维护一个线程池，并且需要自己去包装一个又一个的任务，同时需要自己去调度线程执行任务并维护上下文切换，这一切通常会耗费程序员大量的心智。那么能不能有一种机制，程序员只需要定义很多个任务，让系统去帮助我们把这些任务分配到CPU上实现并发执行呢？

Go语言中的`goroutine`就是这样一种机制，`goroutine`的概念类似于线程，但 `goroutine`是由Go的运行时（runtime）调度和管理的。Go程序会智能地将 goroutine 中的任务合理地分配给每个CPU。Go语言之所以被称为现代化的编程语言，就是因为它在语言层面已经内置了调度和上下文切换的机制。

在Go语言编程中你不需要去自己写进程、线程、协程，你的技能包里只有一个技能–`goroutine`，当你需要让某个任务并发执行的时候，你只需要把这个任务包装成一个函数，开启一个`goroutine`去执行这个函数就可以了，就是这么简单粗暴。

#### goroutine使用

```go
func hello(){
	fmt.Println("Hello Goroutine!")
}

func main(){
	go hello()
	fmt.Println("main goroutine done!")
	time.Sleep(time.Second)
  //如果没用time.Sleep，则最后输出只有main goroutine done!。这是因为在main被执行完后，程序就结束了，而为hello()创建新的goroutine还需要一段时间，这里被直接切断了
}
```

#### 启动多个goroutine

```go
var wg sync.WaitGroup

func hello(i int){
	defer wg.Done()//goroutine结束就登记-1
	fmt.Println("Hello Goroutine!",i)
}

func main(){
	for i:=0;i<10;i++{
		wg.Add(1)//启动一个goroutine就登记+1
		go hello(i)
	}
	wg.Wait()//等待所有登记的goroutine全部都结束
}
```

## 二. 底层实现逻辑

### 1. 线程模型

#### 用户级线程模型 (python中的协程)

<img src="https://segmentfault.com/img/remote/1460000018150990/view" alt="preview" style="zoom:50%;" />

如图所示，多个用户态的线程对应着一个内核线程，程序线程的创建、终止、切换或者同步等线程工作必须自身来完成。它可以做快速的上下文切换。缺点是不能有效利用多核CPU。

#### 内核级线程模型 (C++中的线程调用)

<img src="https://segmentfault.com/img/remote/1460000018150991/view" alt="preview" style="zoom:50%;" />

这种模型直接调用操作系统的内核线程，所有线程的创建、终止、切换、同步等操作，都由内核来完成。一个用户态的线程对应一个系统线程，它可以利用多核机制，但上下文切换需要消耗额外的资源。C++就是这种。

#### 两级线程模型

<img src="https://segmentfault.com/img/remote/1460000018150992/view" alt="preview" style="zoom:50%;" />

这种模型是介于用户级线程模型和内核级线程模型之间的一种线程模型。这种模型的实现非常复杂，和内核级线程模型类似，一个进程中可以对应多个内核级线程，但是进程中的线程不和内核线程一一对应；这种线程模型会先创建多个内核级线程，然后用自身的用户级线程去对应创建的多个内核级线程，自身的用户级线程需要本身程序去调度，内核级的线程交给操作系统内核去调度。

M个用户线程对应N个系统线程，缺点增加了调度器的实现难度。

**Go语言的线程模型就是一种特殊的两级线程模型(GPM调度模型)。**

注：类似于线程池技术？

### 2. Golang调度器之GMP模型

多线程或多进程是并行的基本条件，但单线程也可以用协程(coroutine)做到并发。简单将Goroutine归纳为协程并不合适，因为它运行时会创建多个线程来执行并发任务，且任务单元可被调度到其它线程执行。这更像是多线程和协程的结合体，能最大限度提升执行效率，发挥多核处理器能力。

#### G

G是Goroutine的缩写，相当于操作系统中的进程控制块，在这里就是Goroutine的控制结构，是对Goroutine的抽象。其中包括执行的函数指令及参数；G保存的任务对象；线程上下文切换，现场保护和现场恢复需要的寄存器(SP、IP)等信息。

#### M

M是一个线程或称为Machine，所有M是有线程栈的。如果不对该线程栈提供内存的话，系统会给该线程栈提供内存(不同操作系统提供的线程栈大小不同)。当指定了线程栈，则M.stack→G.stack，M的PC寄存器指向G提供的函数，然后去执行。

#### P

P(Processor)是一个抽象的概念，并不是真正的物理CPU。所以当P有任务时需要创建或者唤醒一个系统线程来执行它队列里的任务。所以P/M需要进行绑定，构成一个执行单元。

P决定了同时可以并发任务的数量，可通过GOMAXPROCS限制同时执行用户级任务的操作系统线程。可以通过runtime.GOMAXPROCS进行指定。在Go1.5之后GOMAXPROCS被默认设置可用的核数，而之前则默认为1。

#### Go调度器调度过程

首先创建一个G对象，G对象保存到P本地队列或者是全局队列。P此时去唤醒一个M。P继续执行它的执行序。M寻找是否有空闲的P，如果有则将该G对象移动到它本身。接下来M执行一个调度循环(调用G对象->执行->清理线程→继续找新的Goroutine执行)。

M执行过程中，随时会发生上下文切换。当发生上线文切换时，需要对执行现场进行保护，以便下次被调度执行时进行现场恢复。Go调度器M的栈保存在G对象上，只需要将M所需要的寄存器(SP、PC等)保存到G对象上就可以实现现场保护。当这些寄存器数据被保护起来，就随时可以做上下文切换了，在中断之前把现场保存起来。如果此时G任务还没有执行完，M可以将任务重新丢到P的任务队列，等待下一次被调度执行。当再次被调度执行时，M通过访问G的vdsoSP、vdsoPC寄存器进行现场恢复(从上次中断位置继续执行)。

### 3. 关键词语

#### (1) P队列

通过上图可以发现，P有两种队列：本地队列和全局队列。

- **本地队列：** 当前P的队列，本地队列是Lock-Free，没有数据竞争问题，无需加锁处理，可以提升处理速度。
- **全局队列：**全局队列为了保证多个P之间任务的平衡。所有M共享P全局队列，为保证数据竞争问题，需要加锁处理。相比本地队列处理速度要低于全局队列。

#### (2) 上下文切换

简单理解为当时的环境即可，环境可以包括当时程序状态以及变量状态。例如线程切换的时候在内核会发生上下文切换，这里的上下文就包括了当时寄存器的值，把寄存器的值保存起来，等下次该线程又得到cpu时间的时候再恢复寄存器的值，这样线程才能正确运行。

对于代码中某个值说，上下文是指这个值所在的局部(全局)作用域对象。相对于进程而言，上下文就是进程执行时的环境，具体来说就是各个变量和数据，包括所有的寄存器变量、进程打开的文件、内存(堆栈)信息等。

#### (3) 线程清理

Goroutine被调度执行必须保证P/M进行绑定，所以线程清理只需要将P释放就可以实现线程的清理。什么时候P会释放，保证其它G可以被执行。P被释放主要有两种情况。

- 主动释放：最典型的例子是，当执行G任务时有系统调用，当发生系统调用时M会处于Block状态。调度器会设置一个超时时间，当超时时会将P释放。
- **被动释放：**如果发生系统调用，有一个专门监控程序，进行扫描当前处于阻塞的P/M组合。当超过系统程序设置的超时时间，会自动将P资源抢走。去执行队列的其它G任务。

终于要来说说Golang中最吸引人的goroutine了，这也是Golang能够横空出世的主要原因。不同于Python基于进程的并发模型，以及C++、Java等基于线程的并发模型。Golang采用轻量级的goroutine来实现并发，可以大大减少CPU的切换。现在已经有太多的文章来介绍goroutine的用法，在这里，我们从源码的角度来看看其内部实现。

### 4. Go语言如何去实现两级线程模型的？

`M`指的是`Machine`，**一个`M`直接关联了一个内核线程**。由操作系统管理。

`P`指的是”processor”，代表了`M`所需的上下文环境，也是处理用户级代码逻辑的处理器。它负责衔接M和G的调度上下文，将等待执行的G与M对接，它是实现从N:1到N:M映射的关键。

`G`指的是`Goroutine`，其实本质上也是一种轻量级的线程。包括了调用栈，重要的调度信息，例如channel等。

![preview](https://segmentfault.com/img/remote/1460000018150994?w=1712&h=824/view)

![preview](https://segmentfault.com/img/remote/1460000018150993?w=400&h=391/view)

图中看，有2个物理线程M，每一个M都拥有一个context（P），每一个也都有一个正在运行的goroutine。

图中灰色的那些goroutine并没有运行，而是出于ready的就绪态，正在等待被调度。P维护着这个队列（称之为runqueue），

Go语言里，启动一个goroutine很容易：go function 就行，所以每有一个go语句被执行，runqueue队列就在其末尾加入一个goroutine，一旦上下文运行goroutine直到调度点，它会从其runqueue中弹出goroutine，设置堆栈和指令指针并开始运行goroutine。

P的数量由环境变量中的`GOMAXPROCS`决定，通常来说它是和核心数对应，例如在4Core的服务器上回启动4个线程。G会有很多个，每个P会将Goroutine从一个就绪的队列中做Pop操作，为了减小锁的竞争，通常情况下每个P会负责一个队列。

#### GOMAXPROCS

Go语言中可以通过`runtime.GOMAXPROCS()`函数设置当前程序并发时占用的CPU逻辑核心数。

Go1.5版本之前，默认使用的是单核心执行。Go1.5版本之后，默认使用全部的CPU逻辑核心数。

举例：

```go
func a(){
	for i:=1;i<100;i++{
		fmt.Println("A:",i)
	}
}

func b(){
	for i:=1;i<100;i++{
		fmt.Println("B:",i)
	}
}

func test1(amount int){
	runtime.GOMAXPROCS(amount)
	go a()
	go b()
	time.Sleep(time.Second)
}

func main(){
	test1(1)
}
```

其中amount代表逻辑核心的数量，当amount=1时，则是做完一个任务再做另一个任务；当设置为2时，则是两个任务并行执行

#### P（Processor）的作用

为什么一定需要一个上下文，我们能不能直接除去上下文，让`Goroutine`的`runqueues`挂到M上呢？答案是不行，需要上下文的目的，是当遇到内核线程阻塞的时候，我们可以直接放开其他线程。

一个很简单的例子就是系统调用`sysall`，一个线程肯定不能在系统调用被阻塞时同时执行代码，这个时候，此线程M需要放弃当前的上下文环境P，以便可以让其他的`Goroutine`被调度执行。

![img](https://segmentfault.com/img/remote/1460000018150995)

如上图左图所示，M0中的G0执行了syscall （IO阻塞了），然后就创建了一个M1(也有可能来自线程缓存)，（转向右图）然后M0丢弃了P，等待syscall的返回值，M1接受了P，将·继续执行`Goroutine`队列中的其他`Goroutine`。

当系统调用syscall结束后，M0会“偷”一个上下文，如果不成功，M0就把它的Gouroutine G0放到一个全局的runqueue中，将自己置于线程缓存中并进入休眠状态。全局runqueue是各个P在运行完自己的本地的Goroutine runqueue后用来拉取新goroutine的地方。P也会周期性的检查这个全局runqueue上的goroutine，否则，全局runqueue上的goroutines可能得不到执行而饿死。

#### 均衡的分配工作

按照以上的说法，上下文P会定期的检查全局的goroutine 队列中的goroutine，以便自己在消费掉自身Goroutine队列的时候有事可做。假如全局goroutine队列中的goroutine也没了呢？就从其他运行的中的P的runqueue里偷。

每个P中的`Goroutine`不同导致他们运行的效率和时间也不同，在一个有很多P和M的环境中，不能让一个P跑完自身的`Goroutine`就没事可做了，因为或许其他的P有很长的`goroutine`队列要跑，得需要均衡。
该如何解决呢？

Go的做法倒也直接，从其他P中偷一半！

![img](https://segmentfault.com/img/remote/1460000018150996)

### 5. 底层代码的实现

参考 https://www.cnblogs.com/sunsky303/p/9705727.html

## 三. 实战部分

### 1. Channel

单纯地将函数并发执行是没有意义的。函数与函数间需要交换数据才能体现并发执行函数的意义。

虽然可以使用共享内存进行数据交换，但是共享内存在不同的`goroutine`中容易发生竞态问题。为了保证数据交换的正确性，必须使用互斥量对内存进行加锁，这种做法势必造成性能问题。

Go语言的并发模型是`CSP（Communicating Sequential Processes）`，提倡**通过通信共享内存**而不是**通过共享内存而实现通信**。

如果说`goroutine`是Go程序并发的执行体，`channel`就是它们之间的连接。`channel`是可以让一个`goroutine`发送特定值到另一个`goroutine`的通信机制。

Go 语言中的通道（channel）是一种特殊的类型。通道像一个传送带或者队列，总是遵循先入先出（First In First Out）的规则，保证收发数据的顺序。每一个通道都是一个具体类型的导管，也就是声明channel的时候需要为其指定元素类型。

#### channel类型声明

`channel`是一种类型，一种引用类型。声明通道类型的格式如下：

```go
var 变量 chan 元素类型
```

举几个例子

```go
var ch1 chan int   // 声明一个传递整型的通道
var ch2 chan bool  // 声明一个传递布尔型的通道
var ch3 chan []int // 声明一个传递int切片的通道
```

#### 创建channel

通道是引用类型，通道类型的空值是`nil`。声明的通道后需要使用`make`函数初始化之后才能使用。

例子：

```go
ch4 := make(chan int,[缓冲大小]) //其中缓冲大小是可选项
ch5 := make(chan bool)
ch6 := make(chan []int)
```

#### channel的操作

通道有发送（send）、接收(receive）和关闭（close）三种操作。

发送和接收都使用`<-`符号。

```go
ch:=make(chan int)
ch<-10
x:=<-ch
fmt.Println(x)
close(ch)
```

关于关闭通道需要注意的事情是，只有在通知接收方goroutine所有的数据都发送完毕的时候才需要关闭通道。通道是可以被垃圾回收机制回收的，它和关闭文件是不一样的，在结束操作之后关闭文件是必须要做的，但关闭通道不是必须的。

关闭后的通道有以下特点：

1. 对一个关闭的通道再发送值就会导致panic。
2. 对一个关闭的通道进行接收会一直获取值直到通道为空。
3. 对一个关闭的并且没有值的通道执行接收操作会得到对应类型的零值。
4. 关闭一个已经关闭的通道会导致panic。

#### 无缓冲的通道

无缓冲的通道又称为阻塞的通道。无缓冲的通道只有在有人接收值的时候才能发送值。就像你住的小区没有快递柜和代收点，快递员给你打电话必须要把这个物品送到你的手中，简单来说就是无缓冲的通道必须有接收才能发送。

```go
func recv(c chan int){
	ret:=<-c
	fmt.Println(ret)
}

func main(){
	ch:=make(chan int)
	go recv(ch)
	ch<-10
	fmt.Println("send successfully")
}
```

无缓冲通道上的发送操作会阻塞，直到另一个`goroutine`在该通道上执行接收操作，这时值才能发送成功，两个`goroutine`将继续执行。相反，如果接收操作先执行，接收方的goroutine将阻塞，直到另一个`goroutine`在该通道上发送一个值。

使用无缓冲通道进行通信将导致发送和接收的`goroutine`同步化。因此，无缓冲通道也被称为`同步通道`。

#### 有缓冲的通道

解决上面问题的方法还有一种就是使用有缓冲区的通道。我们可以在使用make函数初始化通道的时候为其指定通道的容量，例如：

```go
func main(){
  ch:=make(chan int 1)
  ch<-10
  fmt.Println("发送成功")
}
```

只要通道的容量大于零，那么该通道就是有缓冲的通道，通道的容量表示通道中能存放元素的数量。就像你小区的快递柜只有那么个多格子，格子满了就装不下了，就阻塞了，等到别人取走一个快递员就能往里面放一个。

我们可以使用内置的`len`函数获取通道内元素的数量，使用`cap`函数获取通道的容量，虽然我们很少会这么做。

#### for range从通道循环取值

```go
func main(){
	ch1:=make(chan int)
	ch2:=make(chan int)
	go func(){
		for i:=0;i<100;i++{
			ch1<-i
		}
		close(ch1)
	}()
	go func(){
		for {
			i,ok:=<-ch1//通道关闭后再取值的话则ok=false
			if !ok{
				break
			}
			ch2<-i*i
		}
		close(ch2)
	}()

	for i:= range ch2{ //通道关闭后会退出for range循环
		fmt.Println(i)
	}
}
```

#### 单向通道

有的时候我们会将通道作为参数在多个任务函数间传递，很多时候我们在不同的任务函数中使用通道都会对其进行限制，比如限制通道在函数中只能发送或只能接收。

Go语言中提供了**单向通道**来处理这种情况，其中：

- `chan<- int`是一个只写单向通道（只能对其写入int类型值），可以对其执行发送操作但是不能执行接收操作；
- `<-chan int`是一个只读单向通道（只能从其读取int类型值），可以对其执行接收操作但是不能执行发送操作。

例子：

```go
func counter(out chan<- int) {
	for i := 0; i < 100; i++ {
		out <- i
	}
	close(out)
}

func squarer(out chan<- int, in <-chan int) {
	for i := range in {
		out <- i * i
	}
	close(out)
}
func printer(in <-chan int) {
	for i := range in {
		fmt.Println(i)
	}
}

func main() {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go counter(ch1)
	go squarer(ch2, ch1)
	printer(ch2)
}
```

在函数传参及任何赋值操作中可以将双向通道转换为单向通道，但反过来是不可以的。

#### 利用通道实现生产者消费者

```go
var wg sync.WaitGroup
func producer(ch1 chan<- string,pNum int){
	for i:=0;i<10;i++{
		content:=fmt.Sprintf("item %v from p%v", i,pNum)
		fmt.Println(content)
		ch1<-content
	}
	wg.Done()
}

func customer(ch1 <-chan string,cNum int){
	for i:=0;i<10;i++{
		v:=<-ch1
		fmt.Printf("c%v received: %v\n",cNum,v)
	}
	wg.Done()
}

func main(){
	ch:=make(chan string,5)
	for i:=1;i<=5;i++{
		wg.Add(1)
		go producer(ch,i)
		wg.Add(1)
		go customer(ch,i)
	}
	wg.Wait()
}
```

#### 小结

<img src="https://www.liwenzhou.com/images/Go/concurrence/channel01.png" alt="channel异常总结" style="zoom:50%;" />

关闭已经关闭的`channel`也会引发`panic`。

### 2. worker pool（goroutine 池）

在工作中我们通常会使用可以指定启动的goroutine数量–`worker pool`模式，控制`goroutine`的数量，防止`goroutine`泄漏和暴涨。

一个简易的`work pool`示例代码如下：

```go
func worker(id int, jobs <-chan int, results chan<- int){
	for j:=range jobs{
		fmt.Printf("worker:%d start job:%d\n",id,j)
		time.Sleep(time.Second)
		fmt.Printf("worker:%d end job:%d\n",id,j)
		results<-j*2
	}
}

func main(){
	jobs:=make(chan int, 100)
	results:=make(chan int,100)
	//开启三个goroutine
	for w:=1;w<=3;w++{
		go worker(w,jobs,results)
	}
	//5个任务
	for j:=1;j<=5;j++{
		jobs<-j
	}
	close(jobs)
	for a:=1;a<=5;a++{
		<-results
	}
}
```

### 3. select 多路复用

在某些场景下我们需要同时从多个通道接收数据。通道在接收数据时，如果没有数据可以接收将会发生阻塞。

`select`的使用类似于switch语句，它有一系列case分支和一个默认的分支。每个case会对应一个通道的通信（接收或发送）过程。`select`会一直等待，直到某个`case`的通信操作完成时，就会执行`case`分支对应的语句。具体格式如下：

```go
func main(){
	ch:=make(chan int,1)
	for i:=0;i<10;i++{
		select{
		case x:=<-ch:
			fmt.Println(x)
		case ch<-i:
		}
	}
```

- 可处理一个或多个channel的发送/接收操作。
- 如果多个`case`同时满足，`select`会随机选择一个。
- 对于没有`case`的`select{}`会一直等待，可用于阻塞main函数。

### 4. 并发安全和锁

有时候在Go代码中可能会存在多个`goroutine`同时操作一个资源（临界区），这种情况会发生`竞态问题`（数据竞态）。类比现实生活中的例子有十字路口被各个方向的的汽车竞争；还有火车上的卫生间被车厢里的人竞争。

例：

```go
var x int64
var wg sync.WaitGroup

func add(){
	for i:=0;i<5000;i++{
		x=x+1
	}
	wg.Done()
}

func main(){
	wg.Add(2)
	go add()
	go add()
	wg.Wait()
	fmt.Println(x)
}
```

上面的代码中我们开启了两个`goroutine`去累加变量x的值，这两个`goroutine`在访问和修改`x`变量的时候就会存在数据竞争，导致最后的结果与期待的不符。

#### 互斥锁

互斥锁是一种常用的控制共享资源访问的方法，它能够保证同时只有一个`goroutine`可以访问共享资源。Go语言中使用`sync`包的`Mutex`类型来实现互斥锁。 使用互斥锁来修复上面代码的问题：

```go
var x int64
var wg sync.WaitGroup
var lock sync.Mutex

func add(){
	for i:=0;i<5000;i++{
		lock.Lock()
		x=x+1
		lock.Unlock()
	}
	wg.Done()
}

func main(){
	wg.Add(2)
	go add()
	go add()
	wg.Wait()
	fmt.Println(x)
}
```

使用互斥锁能够保证同一时间有且只有一个`goroutine`进入临界区，其他的`goroutine`则在等待锁；当互斥锁释放后，等待的`goroutine`才可以获取锁进入临界区，多个`goroutine`同时等待一个锁时，唤醒的策略是随机的。

#### 读写互斥锁

互斥锁是完全互斥的，但是有很多实际的场景下是读多写少的，当我们并发的去读取一个资源不涉及资源修改的时候是没有必要加锁的，这种场景下使用读写锁是更好的一种选择。读写锁在Go语言中使用`sync`包中的`RWMutex`类型。

读写锁分为两种：读锁和写锁。当一个goroutine获取读锁之后，其他的`goroutine`如果是获取读锁会继续获得锁，如果是获取写锁就会等待；当一个`goroutine`获取写锁之后，其他的`goroutine`无论是获取读锁还是写锁都会等待。

```go
var(
	x int64
	wg sync.WaitGroup
	lock sync.Mutex
	rwlock sync.RWMutex
)

func write(){
	rwlock.Lock()//加写锁
	x=x+1
	time.Sleep(10*time.Millisecond)
	rwlock.Unlock()
	wg.Done()
}

func read(){
	rwlock.RLock()
	time.Sleep(time.Millisecond)
	rwlock.RUnlock()
	wg.Done()
}

func main(){
	start:=time.Now()
	for i:=0;i<10;i++{
		wg.Add(1)
		go write()
	}
	for i:=0;i<1000;i++{
		wg.Add(1)
		go read()
	}
	wg.Wait()
	end:=time.Now()
	fmt.Println(end.Sub(start))
}
```

需要注意的是读写锁非常适合读多写少的场景，如果读和写的操作差别不大，读写锁的优势就发挥不出来。



### 5. Sync包

#### Waitgroup (类似信号量)

| 方法名                          | 功能                |
| ------------------------------- | ------------------- |
| (wg * WaitGroup) Add(delta int) | 计数器+delta        |
| (wg *WaitGroup) Done()          | 计数器-1            |
| (wg *WaitGroup) Wait()          | 阻塞直到计数器变为0 |

`sync.WaitGroup`内部维护着一个计数器，计数器的值可以增加和减少。例如当我们启动了N 个并发任务时，就将计数器值增加N。每个任务完成时通过调用Done()方法将计数器减1。通过调用Wait()来等待并发任务执行完，当计数器值为0时，表示所有并发任务已经完成。

```go
//测试当WaitGroup为0时会发生什么
```

