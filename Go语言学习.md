## Go语言文件的基本结构

```go
package main

//导入语句， 导入的包要用双引号来包裹
import "fmt"

//函数外只能放置标识符（变量，常量，函数，类型）的申明

//程序的入口
func main(){
	fmt.Println("Hello world!")
}
```

## 变量申明

```go
package main

import "fmt"
//申明变量
var num int

//推荐使用驼峰式的命名
//批量申明变量
var (
	name string //""
	age int  //0
	isOk bool //false
)

func foo()(int,string){
	return 10,"test"
}

func main(){
	name="lixiang"
	age=18
	isOk=true
	//var heiheihei string 非全局变量申明后必须使用，不然编译时会报错
	fmt.Printf("name:%s \n",name)
	fmt.Println(age)//打印完指定内容自动加换行符

	//申明变量同时赋值
	var s1 string="xiaowang"
	fmt.Println(s1)
	//类型推到(根据值判断对象是什么类型)
	var s2="xiaoli"
	fmt.Println(s2)
	//短变量申明,只能在函数中使用
	s3:="xiaoma"
	fmt.Println(s3)
	//匿名变量,用_来接收返回值
	_,y:=foo()
	println(y)
	//同个作用域中不能重复申明同一个变量
}
```

## Go常量

### 常量定义

```go
package main
import "fmt"
//常量
//定义了常量之后不能修改
const pi=3.14159
//批量申明常量
const (
	STATUSOK=200
	NOTFOUND=404
)
//批量申明常量时，如果某一行没有赋值，默认和上一行一致
const (
	n1=100
	n2
	n3
)
func main(){
	fmt.Println(n1)
	fmt.Println(n2)
	fmt.Println(n3)
}
```

### iota

是go语言中的常量计数器，只能在常量的表达式中使用

在关键词出现时将被重置为0，每新增一行iota计数一次

## Go 不同类型

### Go整形

Go本身不能定义二进制数，通过整形去转换

```go
package main

import "fmt"

//整形
func main(){
	var i1=101
	fmt.Printf("%d\n",i1)
	fmt.Printf("%b\n",i1)
	i2:=077
	fmt.Printf("%d\n",i2)
	i3:= 0x1234567
	fmt.Printf("%d\n",i3)
	fmt.Printf("%T\n",i3)
	//申明int8类型的变量
	i4:=int8(9)
	fmt.Printf("%T\n",i4)
	i5:=int16(9)
	fmt.Printf("%T\n",i5)
}
```

### 浮点型

float32与float64

```go
package main

import (
	"fmt"
)

//浮点数
func main(){
	f1:=1.2345
	fmt.Printf("%T\n",f1) //默认Go语言中的小数都是float64类型
	f2:=float32(1.233423)
	fmt.Printf("%T\n",f2) //默认Go语言中的小数都是float64类型
	//f2的值不能
```

### 布尔值

默认为false

```go
  package main

  import "fmt"
  func main(){
	  b1:=true
	  var b2 bool
	  fmt.Printf("%T\n",b1)
	  fmt.Printf("%T\n",b2)
	  fmt.Printf("%v\n",b1)
	  fmt.Printf("%v\n",b2)
  }
```

### 字符串

Go语言中的字符串必须用“ ”来包裹的

Go语言中的单引号包裹的是字符

```go
package main

import (
	"fmt"
	"strings"
)

func main(){
	s:="Hello"
	c1:='杀'
	c2:="h"
	fmt.Printf("%T\n",s)
	fmt.Printf("%T\n",c1)
	fmt.Printf("%T\n",c2)
	//多行的字符串
	//反引号内部原样输出
	s2:=`test1
test2
test3
test4
	`
	fmt.Println(s2)
	//字符串的常用操作
	fmt.Println(len(s2))
	name:="lixiang"
	age:="14"
	fmt.Println(name+age)
	ss1:=fmt.Sprintf("%s%s",name,age)
	fmt.Printf("%s\n",ss1)
	s3:="E:\\Desktop\\Test\\MaHaoYe\\HelloWorld"
	//分割
	ret:=strings.Split(s3,"\\")
	fmt.Println(ret)
	//包含
	fmt.Println(strings.Contains(ss1,"lixing"))
	fmt.Println(strings.Contains(ss1,"lixiang"))
	//前缀
	fmt.Println(strings.HasPrefix(ss1,"li"))
	//后缀
	fmt.Println(strings.HasSuffix(ss1,"xiang"))
	//子串出现的位置
	s4:="abcdeb"
	fmt.Println(strings.Index(s4,"c"))
	fmt.Println(strings.LastIndex(s4,"b"))
}
```

## 流程控制

### if 语句

```go
package main
import "fmt"

func main(){
	age:=19
	if age>35{
		fmt.Println("澳门首家线上赌场开业了！")
	}else if age>10{
		fmt.Println("青年")
	}else{
		fmt.Println("该写暑假作业了！")
	}
}
```

### for 语句

```go
package main
import "fmt"

func main(){
	//基本格式
	for i:=0;i<10;i++{
		fmt.Println(i)
	}
	//变种1
	i:=5
	for ;i<10;i++{
		fmt.Println(i)
	}
	//变种2
	for ;i<10;{
		fmt.Println(i)
		i++
	}
	//for range(键值循环)
	s:="hello"
	for _,v:=range s{
		fmt.Printf("%c\n",v)
	}
}
```

switch语句

```go
package main

import "fmt"
//break & continue
func main(){
	/*for i:=0;i<10;i++{
		if i==5{
			continue
		}
		fmt.Println(i)
	}*/
	var n=3
	switch n{
	case 1:fmt.Println("daMuZhi")
	case 2:fmt.Println("shiZhi")
	case 3:fmt.Println("zhongZhi")
	default:
		fmt.Println("error")
	}
}
```

## Go语言中的数据类型

### 数组(Array)

```go
package main

import "fmt"

func modifyArray(x [3]int){
	x[0]=100
}
func modifyArray2(x [3][2]int){
	x[2][0]=100
}

func main(){
	//数组初始化
	var testArray [3]int
	fmt.Println(testArray)
	var numArray=[3]int{1,2}
	fmt.Println(numArray)
	var cityArray=[...]string{"beijing","shanghai","chongqing"}
	fmt.Println((cityArray))
	a:=[...]int{1:1,3:5}
	fmt.Printf("%T\n",a)
	//数组遍历
	//for 循环遍历
	for i:=0;i<len(a);i++{
		fmt.Println(a[i])
	}
	//for range遍历
	for index, value:=range a{
		fmt.Println(index,value)
	}
	//多维数组
	a1:=[3][2]string{
		{"beijing","shanghai"},
		{"guangzhou","shenzheng"},
		{"chengdu","chongqing"},
	}
	fmt.Println(a1)
	//多维数组的遍历
	for _,v1:=range a1 {
		for _,v2:=range v1{
			fmt.Printf("%s\t",v2)
		}
		fmt.Println()
	}
	a3:=[3]int{10,20,30}
	modifyArray(a3)
	fmt.Println(a3)
	b:=[3][2]int{
		{1,1},
		{1,1},
		{1,1},
	}
	modifyArray2(b)
	fmt.Println(b)
}
```

### 切片(Slice)

切片（Slice）是一个拥有相同类型元素的可变长度的序列。它是基于数组类型做的一层封装。它非常灵活，支持自动扩容。

切片是一个引用类型，它的内部结构包含`地址`、`长度`和`容量`。切片一般用于快速地操作一块数据集合。

基本声明语法

var name []T 

#### 切面的本质

切片的本质就是对底层数组的封装，它包含了三个信息：底层数组的指针、切片的长度（len）和切片的容量（cap）。

举个例子，现在有一个数组`a := [8]int{0, 1, 2, 3, 4, 5, 6, 7}`，切片`s1 := a[:5]`，相应示意图如下。<img src="https://www.liwenzhou.com/images/Go/slice/slice_01.png" alt="slice_01" style="zoom:50%;" />

切片`s2 := a[3:6]`，相应示意图如下：<img src="https://www.liwenzhou.com/images/Go/slice/slice_02.png" alt="slice_02" style="zoom:50%;" />

对数组的进一步封装，其包含了一个指向底层数组的指针，长度len() 和容量cap()

#### 切片表达式

```go
//简单的切片表达式
func main(){
	a:=[5]int{1,2,3,4,5}
	s:=a[1:3] //闭开区间
	a[1]=9 //s的切片本质也是一个新的指针，而不是复制了a的范围，切片的上限确定了len的长度
	fmt.Printf("s:%v len(s): %v cap(s): %v\n",s,len(s),cap(s))
	s2:=s[3:4]
	fmt.Printf("s2:%v len(s): %v cap(s): %v\n",s2,len(s2),cap(s2))
}
```

```go
//完整的切片表达式
func main(){
	a:=[5]int{1,2,3,4,5}
	t:=a[1:3:5]
	fmt.Printf("s:%v len(s): %v cap(s): %v\n",t,len(t),cap(t))
}
```

#### 使用make()函数构造切片

make([]T,size,cap)

```go
func main(){
	a:=make([]int,2,10)
	fmt.Println(a)
	fmt.Println(len(a))
	fmt.Println(cap(a))
}
```

#### 判断切片是否为空

利用len(s)==0，而不是用s==nil来判断

切片共享底层数组，所以一个切片的修改会影响另一个切片的内容

#### 切片的遍历

```go
func main(){
  a:=[]int{1,3,5}
	for i:=0;i<len(a);i++{
		fmt.Println(i,a[i])
	}
	for index,value:=range a{
		fmt.Println(index,value)
	}
}
```

#### 利用append()为切片添加元素

```go
func main(){
	var s []int
	s=append(s,1)
	s=append(s,1,2,3,4)
	s2:=[]int{5,6,7}
	s=append(s,s2...)
	fmt.Println(s2)
	fmt.Println(s)
}
```

#### 使用copy()函数来复制切片

```go
	func main(){
	a:=[]int{1,2,3,4,5}
	c:=make([]int,5,5)
	copy(c,a)
	fmt.Println(c)
	a[1]=10000
	fmt.Println(a)
	fmt.Println(c)
}
```

### 指针

Go语言中不存在指针操作

1. &: 取地址

2. *: 根据地址取值

```go
package main
import "fmt"
func main(){
	//1. 取地址
	//2. 根据地址取值
	n:=18
	a:=&n
	fmt.Println(&n)
	fmt.Println(a)
	fmt.Printf("%T\n",a)
	m:=*a //*a表示int类型的指针
	fmt.Printf("%T\n",m)
}
```

我们来看一下`b := &a`的图示：

<img src="https://www.liwenzhou.com/images/Go/pointer/ptr.png" alt="取变量地址图示" style="zoom:50%;" />

### new 和 make

new是给基本数据类型分配内存的：int/bool/float等（比较少用）返回的是对应类型的指针

make是用来给slice，map，channel等申请内存的，make函数返回的是对应的这三个类型本身 

```go
package main
import "fmt"
/*func main(){
	//1. 取地址
	//2. 根据地址取值
	n:=18
	a:=&n
	fmt.Println(&n)
	fmt.Println(a)
	fmt.Printf("%T\n",a)
	m:=*a //*a表示int类型的指针
	fmt.Printf("%T\n",m)
}*/
func main(){
	//var a *int //未初始化，为一个nil
	//new申请一个内存地址
	var a=new(int)
	*a=100
	fmt.Println(a)
	var a2 *int
	fmt.Println(a2)
}
```

### map

Go语言中提供的映射关系容器为`map`，其内部使用`散列表（hash）`实现。

map是一种无序的基于`key-value`的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

#### map的基本用法

```Go
//map的基本用法
package main
import "fmt"
func main(){
	scoreMap:=make(map[string]int,8)
	scoreMap["张三"]=90
	scoreMap["小明"]=100
	fmt.Println(scoreMap)
	fmt.Println(scoreMap["小明"])
	fmt.Printf("type of a:%T\n",scoreMap)
}
```

```Go
//声明时填充元素
func main(){
	userInfo:=map[string]string{
		"username":"沙河小王子",
		"password":"123456",
	}
	fmt.Println(userInfo)
}
```

#### 判断某一个键是否存在

```Go
func main(){
	scoreMap:=make(map[string]int,8)
	scoreMap["张三"]=90
	scoreMap["小明"]=100
	v,ok:=scoreMap["小范"]
	if ok {
		fmt.Println(v)
	}else{
		fmt.Println(ok)
		fmt.Println("查无此人")
	}
}
```

#### map的遍历

可以用for range 来进行遍历

```go
func main(){
	scoreMap:=make(map[string]int)
	scoreMap["张三"]=90
	scoreMap["李四"]=100
	scoreMap["小范"]=80
	for k,v:=range scoreMap{
		fmt.Println(k,v)
	}
	for k:=range scoreMap{
		fmt.Println(k)
	}
	//使用delete()函数来删除键值对
	delete(scoreMap,"张三")
	fmt.Println(scoreMap)
}
```

按照指定顺序来遍历map

```go
func main(){
	rand.Seed(time.Now().UnixNano())//初始化随机种子

	var scoreMap=make(map[string]int,200)

	for i:=0;i<100;i++{
		key:=fmt.Sprintf("stu%02d",i)
		value:=rand.Intn(100)
		scoreMap[key]=value
	}
	//取出map中的所有key存入切片keys
	var keys=make([]string,0,200)
	for key:=range scoreMap{
		keys=append(keys,key)
	}
	//对切片进行排序
	sort.Strings(keys)
	for _,key:=range keys{
		fmt.Println(key,scoreMap[key])
	}
}
```

#### 值为切片类型的map

```go
func main(){
	var sliceMap=make(map[string][]string,3)
	fmt.Println(sliceMap)
	fmt.Println("after init")
	key:="中国"
	value,ok:=sliceMap[key]
	if !ok{
		value=make([]string,0,2)
	}
	value=append(value,"北京","上海")
	sliceMap[key]=value
	fmt.Println(sliceMap)
}
```

## 函数与结构体

### 函数

#### 函数的定义

函数的定义与调用

```go
func sayHello(){
	fmt.Println("hello function")
}

func intSum(x int, y int)int{
	return x + y
}

func main(){
	sayHello()
	ret:=intSum(10,20)
	fmt.Println(ret)
}
```

#### 返回值

Go语言中通过`return`关键字向外输出返回值。

多返回值

```go
//多返回值
func calc(x,y int)(int,int){
	sum:=x+y
	sub:=x-y
	return sum,sub
}
//返回值命名
func calc1(x,y int)(sum,sub int){
	sum=x+y
	sub=x-y
	return
}
//返回值补充
func someFunc(x string)[]int {
	if x==""{
		return nil
	}
	ret:=[]int{1,2,3}
	return ret
}

func main(){
	sayHello()
	ret:=intSum(10,20)
	fmt.Println(ret)
	test1,test2:=calc(5,6)
	fmt.Println(test1,test2)
	test3,test4:=calc1(99,8)
	fmt.Println(test3,test4)
	ret1:=someFunc("test")
	fmt.Println(ret1)
}
```

### 函数进阶

#### 变量的作用域

全局变量

```go
//定义全局变量
var num int64=10

func testGlobalVar(){
	fmt.Printf("num%d\n",num)
	fmt.Printf("Typeof the var: %T\n",num)
}
func main(){
	testGlobalVar()
}
```

局部变量

1. 函数内部定义的变量无法在该函数的外部使用
2. 全局变量与局部变量重名，优先访问局部变量
3. 在if, for, switch语句上使用定义变量的方式在语句块外部无法生效

```go
func testLocalVar2(x,y int){
	fmt.Println(x,y)
	if x>0{
		z:=100
		fmt.Println(z) //变量z只有在if语句块内才能生效
	}
	//fmt.Println(z) //此处z变量无法使用
}

func testLocalVar3(){
	for i:=0;i<10;i++{
		fmt.Println(i)
	}
	//fmt.Println(i) //i只能在for语句块内使用
}
```

#### 函数的类型与变量

定义函数的类型

```go
type calculation func(int,int) int
func add(x,y int) int{
	return x+y
}

func sub(x,y int) int{
	return x-y
}

func main(){
	var c calculation
	c=add
	fmt.Printf("Type of c:%T\n",c)
	fmt.Println(c(10,20))
}
```

#### 高阶函数

函数作为参数

```go
func add1(x,y int)int{
	return x+y
}
//函数作为参数
func calc(x,y int,op func(int,int)int)int{
	return op(x,y)
}
func main(){
  ret:=calc(10,20,add1)
  fmt.Println(ret)
}
```

函数作为返回值

```go
//函数作为返回值
func do(s string)(func(int,int)int,error){
	switch s {
	case "+":
		return add,nil
	case "-":
		return sub,nil
	default:
		err:=errors.New("无法识别的操作符")
		return nil,err
	}
}
func main(){
	ret3:=calMethod(90,100)
	fmt.Println(ret3)
}
```

#### 匿名函数

函数当然还可以作为返回值，但是在Go语言中函数内部不能再像之前那样定义函数了，只能定义匿名函数。匿名函数就是没有函数名的函数，匿名函数的定义格式如下：

```go
func(参数)(返回值){
    函数体
}
```

匿名函数因为没有函数名，所以没办法像普通函数那样调用，所以匿名函数需要保存到某个变量或者作为立即执行函数:

```go
func main() {
	// 将匿名函数保存到变量
	add := func(x, y int) {
		fmt.Println(x + y)
	}
	add(10, 20) // 通过变量调用匿名函数

	//自执行函数：匿名函数定义完加()直接执行
	func(x, y int) {
		fmt.Println(x + y)
	}(10, 20)
}
```

匿名函数多用于实现回调函数与闭包

#### 闭包

闭包指的是一个函数和与其相关的引用环境组合而成的实体。简单来说，`闭包=函数+引用环境`。 首先我们来看一个例子：

```go
func adder() func(int) int{
	var x int
	return func(y int) int{
		x+=y
		return x
	}

func main(){
	f:=adder()
	fmt.Println(f(10))
	fmt.Println(f(20))
	fmt.Println(f(30))
	f1:=adder()
	fmt.Println(f1(40))
	fmt.Println(f1(50))
}
```

变量`f`是一个函数并且它引用了其外部作用域中的`x`变量，此时`f`就是一个闭包。 在`f`的生命周期内，变量`x`也一直有效。

```go
//闭包测试1

func adder2(x int) func(int) int{
	return func(y int) int {
		x+=y
		return x
	}
}

func main(){
	var f2=adder2(10)
	fmt.Println(f2(10))
	fmt.Println(f2(50))
}
```

```go
//闭包测试2

func makeSuffixFunc(suffix string) func(string) string{
	return func(name string)string {
		if !strings.HasSuffix(name,suffix){
			return name+suffix
		}
		return name
	}
}

func main(){
	jpgFunc:=makeSuffixFunc(".jpg")
	txtFunc:=makeSuffixFunc(".txt")
	fmt.Println(jpgFunc("test"))
	fmt.Println(txtFunc("test"))
}
```

```go
//闭包测试3
func calc1(base int)(func(int) int,func(int) int){
	add:=func(i int) int{
		base+=i
		return base
	}
	sub:=func(i int) int{
		base-=i
		return base
	}
	return add,sub
}
//这里的base参数是共享的。

func main(){
	f10,f20:=calc1(10)
	fmt.Println(f10(1),f20(2)) //11 9
	fmt.Println(f10(9),f20(3)) //18 15
}
```

#### defer

Go语言中的`defer`语句会将其后面跟随的语句进行延迟处理。在`defer`归属的函数即将返回时，将延迟处理的语句按`defer`定义的逆序进行执行，也就是说，先被`defer`的语句最后被执行，最后被`defer`的语句，最先被执行。

```go
func main(){
	fmt.Println("start")
	defer fmt.Println(1)
	defer fmt.Println(2)
	defer fmt.Println(3)
}

//output
start
3
2
1
```

由于`defer`语句延迟调用的特性，所以`defer`语句能非常方便的处理资源释放问题。比如：资源清理、文件关闭、解锁及记录时间等。

```go
//defer的示例
func f1() int {
	x := 5
	defer func() {
		x++
	}()
	return x
}

func f2() (x int) {
	defer func() {
		x++
	}()
	return 5
}

func f3()(x int){
	defer func(x *int){
		(*x)++
	}(&x)
	return 5
}

func main(){
	fmt.Println(f1())
	fmt.Println(f2())
	fmt.Println(f3())
}
```

```go
func calc(index string,a,b int)int{
	ret:=a+b
	fmt.Println(index,a,b,ret)
	return ret
}

func main(){
	x:=1
	y:=2
	defer calc("AA",x,calc("A",x,y))
	x=10
	defer calc("BB",x,calc("B",x,y))
	y=20
}

//output
A 1 2 3
B 10 2 12
BB 10 12 22
AA 1 3 4
```

### 结构体

Go语言中没有“类”的概念，也不支持“类”的继承等面向对象的概念。Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。

#### 自定义类型与类型别名

自定义类型

```go
type MyInt int
```

类型别名

```go
type TypeAlias=Type
```

区别

```go
//类型定义
type NewInt int
//类型的别名
type MyInt=int
func main(){
	var a NewInt
	var b MyInt
	fmt.Printf("Type of a: %T\n",a)
	fmt.Printf("Type of b: %T\n",b)
}

//output
Type of a: main.NewInt
Type of b: int
//别名在编译过程中不显示名称
```

#### 结构体的定义

使用`type`和`struct`关键字来定义结构体，具体代码格式如下：

```go
type 类型名 struct {
    字段名 字段类型
    字段名 字段类型
    …
}
```

其中：

- 类型名：标识自定义结构体的名称，在同一个包内不能重复。
- 字段名：表示结构体字段名。结构体中的字段名必须唯一。
- 字段类型：表示结构体字段的具体类型。

例子：

```go
//person结构体
type person struct{
	name,city string
	age int8
}

func main(){
	var p1 person
	p1.name="shahe"
	p1.city="beijing"
	p1.age=18
	fmt.Printf("p1=%#v\n",p1)
}
```

#### 匿名结构体

```go
func main(){
	var user struct{Name string;Age int}
	user.Name="xiao wang zi"
	user.Age=18
	fmt.Printf("%#v\n",user)
}
```

#### 创建指针类型结构体

使用`&`对结构体进行取地址操作相当于对该结构体类型进行了一次`new`实例化操作。

```go
//取结构体的地址实体化
func main(){
	p3:=&person{}
	fmt.Printf("%T\n",p3)
	fmt.Printf("p3=%#v\n",p3)
	p3.name="test"
	p3.age=18
	p3.city="shanghai"
	fmt.Printf("p3=%#v\n",p3)
}
```

`p3.name = "test"`其实在底层是`(*p3).name = "test"`，这是Go语言帮我们实现的语法糖。

#### 结构体的初始化

没有初始化的结构体，其成员变量都是对应其类型的零值

```go
type person struct {
	name string
	city string
	age  int8
}

func main() {
	var p4 person
	fmt.Printf("p4=%#v\n", p4) //p4=main.person{name:"", city:"", age:0}
}
```

使用键值对来进行初始化

```go
p5:=person{
  name:"xiaowangzi",
  city:"shanghai",
  age:18
}
```

结构体的内存布局

结构体占用的是一块连续的内存空间

```go
type test struct{
	a int8
	b int8
	c int8
	d int8
}
func main(){
	n:=test{
		1,2,3,4,
	}
	fmt.Printf("n.a:%p\n",&n.a)
	fmt.Printf("n.b:%p\n",&n.b)
	fmt.Printf("n.c:%p\n",&n.c)
	fmt.Printf("n.d:%p\n",&n.d)
}

//output
n.a:0xc0000180f0
n.b:0xc0000180f1
n.c:0xc0000180f2
n.d:0xc0000180f3
```

#### 结构体构造函数

Go语言的结构体没有构造函数，我们可以自己实现。 例如，下方的代码就实现了一个`person`的构造函数。 因为`struct`是值类型，如果结构体比较复杂的话，值拷贝性能开销会比较大，所以该构造函数返回的是结构体指针类型。

```go
type person struct{
	name,city string
	age int8
}

func newPerdson(name,city string, age int8) *person{
	return &person{
		name:name,
		city:city,
		age:age,
	}
}
func main(){
	p9:=newPerdson("zhangsan","shanghai",18)
	fmt.Printf("%#v\n",p9)
}
```

#### 方法与接收者

Go语言中的`方法（Method）`是一种作用于特定类型变量的函数。这种特定类型变量叫做`接收者（Receiver）`。接收者的概念就类似于其他语言中的`this`或者 `self`

```go
func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
    函数体
}
```

其中，

- 接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名称首字母的小写，而不是`self`、`this`之类的命名。例如，`Person`类型的接收者变量应该命名为 `p`，`Connector`类型的接收者变量应该命名为`c`等。
- 接收者类型：接收者类型和参数类似，可以是指针类型和非指针类型。
- 方法名、参数列表、返回参数：具体格式与函数定义相同。

```go
//Person结构体
type person struct{
	name,city string
	age int8
}
//NerPerson 构造函数
func newPerson(name,city string, age int8) *person{
	return &person{
		name:name,
		city:city,
		age:age,
	}
}

//Dream Person的方法
func(p person) Dream(){
	fmt.Printf("%s的梦想是好好学习！\n",p.name)	
}

func main(){
	p9:=newPerson("zhangsan","shanghai",18)
	fmt.Printf("%#v\n",p9)
	p10:=newPerson("小王子","上海",25)
	p10.Dream()
}
```

#### 指针类型的接收者&值类型的接收者

指针类型的接收者由一个结构体的指针组成，由于指针的特性，调用方法时修改接收者指针的任意成员变量，在方法结束后，修改都是有效的。这种方式就十分接近于其他语言中面向对象中的`this`或者`self`。 例如我们为`Person`添加一个`SetAge`方法，来修改实例变量的年龄。

当方法作用于值类型接收者时，Go语言会在代码运行时将接收者的值复制一份。在值类型接收者的方法中可以获取接收者的成员值，但修改操作只是针对副本，无法修改接收者变量本身。

```go
//Dream Person的方法
func(p person) Dream(){
	fmt.Printf("%s的梦想是好好学习！\n",p.name)	
}

//指针类型的接收者
func(p *person) SetAge(newAge int){
	p.age=int8(newAge)
}
//值类型的接收者
func(p person) SetAge2(newAge int){
	p.age=int8(newAge)
}

func main(){
	p9:=newPerson("zhangsan","shanghai",18)
	fmt.Printf("%#v\n",p9)
	p10:=newPerson("小王子","上海",25)
	p10.Dream()
	fmt.Println(p10.age)
	p10.SetAge(30)
	fmt.Println(p10.age)
	p10.SetAge2(40)
	fmt.Println(p10.age)
}
```

应该什么时候使用指针类型的接收者

1. 需要修改接收者中的值
2. 接收者是拷贝代价比较大的大对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。

#### 任意类型添加方法

```go
//MyInt 将int定义为自定义MyInt类型
type MyInt int

//SayHello 为MyInt添加一个SayHello的方法
func (m MyInt) SayHello() {
	fmt.Println("Hello, 我是一个int。")
}
func main() {
	var m1 MyInt
	m1.SayHello() //Hello, 我是一个int。
	m1 = 100
	fmt.Printf("%#v  %T\n", m1, m1) //100  main.MyInt
}
```

#### 结构体的匿名字段

```go
//Person结构体Person类型
type Person struct{
	string
	int
}
func main(){
	p1:=Person{
		"小王子",
		18,
	}
	fmt.Printf("%#v\n",p1)
	fmt.Println(p1.string,p1.int)
}
```

#### 嵌套结构体

```go
//Address 地址结构体
type Address struct{
	Province string
	City string
}
//User 结构体
type User struct{
	Name string
	Gender string
	Address Address
}

func main(){
	user1:=User{
		Name:"zhangsan",
		Gender: "man",
		Address: Address{
			Province: "山东",
			City: "威海",
		},
	}
	fmt.Printf("user-1: %#v\n",user1)
}
```

#### 结构体的继承

Go语言中使用结构体也可以实现其他编程语言中面向对象的继承。

例子: 

```go
type Animal struct{
	name string
}

func (a *Animal)move(){
	fmt.Printf("%s会动!\n",a.name)
}

type Dog struct{
	Feet int8
	*Animal //通过嵌套匿名结构体实现继承
}

func (d *Dog) wang(){
	fmt.Printf("%s会汪汪汪~\n",d.name)
}

func main(){
	d1:=&Dog{
		Feet: 4,
		Animal: &Animal{
			name: "LELE",
		},
	}
	d1.wang()
	d1.move()
}
```

#### 结构体字段的可见性

结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。

#### 结构体与JSON序列化

JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。JSON键值对是用来保存JS对象的一种方式，键/值对组合中的键名写在前面并用双引号`""`包裹，使用冒号`:`分隔，然后紧接着值；多个键值之间使用英文`,`分隔。

```go
type Student struct{
	ID int
	Gender string
	Name string
}

type Class struct{
	Title string
	Students []*Student //切片，传入的是指向student结构体的指针
}

func main(){
	c:=&Class{
		Title: "101",
		Students: make([]*Student,0,200),
	}
	for i:=0;i<10;i++{
		stu:=&Student{
			Name: fmt.Sprintf("stu%02d",i),
			Gender: "Male",
			ID:i,
		}
		c.Students=append(c.Students, stu)
	}
	//JOSON序列化：结构体->JSON格式字符串
	data,err:=json.Marshal(c)
	if err!=nil{
		fmt.Println("json marshal failed")
		return
	}
	fmt.Printf("json:%s\n",data)
	//JSON反序列化：JSON格式的字符串-->结构体
	str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"},{"ID":3,"Gender":"男","Name":"stu03"},{"ID":4,"Gender":"男","Name":"stu04"},{"ID":5,"Gender":"男","Name":"stu05"},{"ID":6,"Gender":"男","Name":"stu06"},{"ID":7,"Gender":"男","Name":"stu07"},{"ID":8,"Gender":"男","Name":"stu08"},{"ID":9,"Gender":"男","Name":"stu09"}]}`
	c1:=&Class{}
	err=json.Unmarshal([]byte(str),c1)
	if err!=nil{
		fmt.Println("json unmarshal failed!")
		return
	}
	fmt.Printf("%#v\n",c1)
}
```

#### 结构体和方法补充

因为slice和map这两种数据类型都包含了指向底层数据的指针，因此我们在需要复制它们时要特别注意。我们来看下面的例子：

```go
type Person2 struct{
	name string
	age int8
	dreams []string
}

func (p *Person2) SetDreams(dreams []string){
	p.dreams=dreams
}

func main(){
	p1:=Person2{name:"xiaowang",age:18}
	data:=[]string{"吃饭","睡觉","打豆豆"}
	p1.SetDreams(data)

	data[1]="不睡觉" //由于传入值是引用项，因此p1中的值也随之发生变化
	fmt.Println(p1.dreams) //[吃饭 不睡觉 打豆豆]
}
```

## Go中的包、接口

### 包：源码复用的基础

`包（package）`是多个Go源码的集合，是一种高级的代码复用方案，Go语言为我们提供了很多内置包，如`fmt`、`os`、`io`等。

#### 定义包

我们还可以根据自己的需要创建自己的包。一个包可以简单理解为一个存放`.go`文件的文件夹。 该文件夹下面的所有go文件都要在代码的第一行添加如下代码，声明该文件归属的包。

```go
package 包名
```

- 一个文件夹下面直接包含的文件只能归属一个`package`，同样一个`package`的文件不能在多个文件夹下。
- 包名可以不和文件夹的名字一样，包名不能包含 `-` 符号。
- 包名为`main`的包为应用程序的入口包，这种包编译后会得到一个可执行文件，而编译不包含`main`包的源代码则不会得到可执行文件。

#### 导入包

...

### 接口

#### 接口类型

基本数据类型(int, float, bool)->结构体->接口(约束这个类型有什么方法)

在Go语言中接口（interface）是一种类型，它规定了变量有哪些方法。

变成中遇到以下场景：不关心一个变量是什么类型，我只关心能调用的方法

```go
type Cat struct{}

func (c Cat) Say() string{
	return "miaomiaomiao"
}

type Dog struct{}

func (d Dog) Say() string{
	return "wangwangwang"
}

func main(){
	c:=Cat{}
	fmt.Println("猫：",c.Say())
	d:=Dog{}
	fmt.Println("狗：",d.Say())
}
```

接口就是即使传不同类型（dog，cat...）同时使用say()来调用

`interface`是一组`method`的集合，是`duck-type programming`的一种体现。接口做的事情就像是定义一个协议（规则），只要一台机器有洗衣服和甩干的功能，我就称它为洗衣机。不关心属性（数据），只关心行为（方法）。

接口举例

```go
//定义一个能叫的类型

type Sayer interface{
	Say() //只要实现了say方法的变量，都是speak类型
}

type Cat struct{}

func (c Cat) Say(){
	fmt.Println("miaomiaomiao")
}

type Dog struct{}

func (d Dog) Say(){
	fmt.Println("wangwangwang")
}

func da(x Sayer){
	x.Say() //打了就叫
}

func main(){
	var c Cat
	var d Dog
	da(c)
	da(d)
}
```

```go
//接口示例2
//不管是什么牌子的车，都能跑

type car interface{
	run()
}

type falali struct{
	brand string
}

func (f falali) run(){
	fmt.Printf("%s速度70迈\n",f.brand)
}

type baoshijie struct{
	brand string
}
func (b baoshijie) run(){
	fmt.Printf("%s速度700迈\n",b.brand)
}

func drive(c car){
	c.run()
}

func main(){
	var f1=falali{
		"法拉利",
	}
	var b1=baoshijie{
		"宝时捷",
	}
	drive(f1)
	drive(b1)
}
```

#### 接口的定义

```go
type 接口名 interface{
  方法名1(参数1,参数2...)(返回值1,返回值2...)
  方法名2(参数1,参数2...)(返回值1,返回值2...)
  ...
}
```

用来给变量\参数\返回值等设置类型

#### 接口的实现

一个变量如果实现了接口中规定的所有的方法，那么这个变量就实现了这个接口->可以称为这个接口类型的变量

面向对象：把方法和数据都放在class里面

面向接口：把方法和数据解耦

```go
type animal interface{
	move()
	eat(sth string)
}

type cat struct{
	name string
	feet int8
}
func (c cat)move(){
	fmt.Println("猫动！")
}
func (c cat)eat(sth string){
	fmt.Printf("%s吃%s\n",c.name,sth)
}

type chicken struct{
	name string
	feet int8
}

func (c chicken)move(){
	fmt.Println("鸡动！")
}
func (c chicken)eat(sth string){
	fmt.Printf("%s吃%s\n",c.name,sth)
}


func main(){
	var a1 animal //定义一个接口类型的变量
	bc:=cat{
		"淘气",
		4,
	} //定义一个cat类型的变量
	a1=bc
	fmt.Println(a1)
	a1.eat("老鼠")
	fmt.Printf("%T\n",a1)
}
```

#### 值接收者和指针接收者实现接口的区别

```go
type Mover interface{
	move()
}

type dog struct{}

//值接收者实现接口
//指针接收者实现接口
func (d *dog) move(){
	fmt.Println("狗会动")
}


func main(){
	var x Mover
	var wangcai=dog{}
	x=wangcai //x不可以接收dog类型，当为指针接收者时
	var fugui=&dog{}
	x=fugui
	x.move()
}
```

#### 类型与接口的关系

```go
type Sayer interface{
	say()
}

type Mover interface{
	move()
}

type dog struct{
	name string
}

//实现Sayer接口
func (d dog)say(){
	fmt.Printf("%s会叫汪汪汪\n",d.name)
}
//实现Mover接口
func (d dog)move(){
	fmt.Printf("%s会动\n",d.name)
}

func main(){
	var x Sayer
	var y Mover
	var a=dog{name:"旺财"}
	x=a
	y=a
	x.say()
	y.move()
}
```

#### 多个类型实现同一接口

```go
type WashingMachine interface{
	wash()
	dry()
}

//甩干器
type dryer struct{}

//实现washingMachine接口的dry()方法
func(d dryer) dry(){
	fmt.Println("甩一甩")
}

//海尔洗衣机
type haier struct{
	name string
	dryer //嵌入甩干机
}
//美的洗衣机
type meidi struct{
	name string
	dryer //嵌入甩干机
}

func (h haier) wash(){
	fmt.Printf("%s洗刷刷\n",h.name)
}
func (h meidi) wash(){
	fmt.Printf("%s洗刷刷\n",h.name)
}

func main(){
	var wm WashingMachine
	h:=haier{name:"海尔"}
	m:=meidi{name:"美的"}
	wm=h
	wm.dry()
	wm.wash()
	wm=m
	wm.dry()
	wm.wash()
}
```

#### 接口嵌套

```go
//Sayer 接口
type Sayer interface{
	say()
}
//Mover 接口
type Mover interface{
	move()
}
//接口嵌套
type animal interface{
	Sayer
	Mover
}

type cat struct{
	name string
}

func (c cat) say(){
	fmt.Println("喵喵喵")
}

func (c cat) move(){
	fmt.Println("猫会动")
}

func main(){
	var x animal
	x=cat{name:"花花"}
	x.move()
	x.say()
}
```

#### 空接口

空接口是指没有定义任何方法的接口。因此任何类型都实现了空接口。

空接口类型的变量可以存储任意类型的变量。

(类似python中的动态类型)

```go
func main(){
	//定义一个空接口
	var a interface{}
	s:="hello shahe"
	a=s
	fmt.Printf("type: %T value: %v\n",a,a)
	i:=100
	a=i
	fmt.Printf("type: %T value: %v\n",a,a)
	j:=false
	a=j
	fmt.Printf("type: %T value: %v\n",a,a)
}
```

#### 空接口的应用

使用空接口实现可以接收任意类型的函数参数。

```go
func show(a interface{}){
	fmt.Printf("type %T value: %v\n",a,a)
}
func main(){
  show(false)
	show("test")
	show(10)
}
```



使用空接口来实现可以保存任意值的字典。

```go
func main(){
  //空接口作为map值
	var studentInfo=make(map[string]interface{})
	studentInfo["name"]="xiaowang"
	studentInfo["age"]=18
	studentInfo["married"]=false
	studentInfo["friends"]=[]string{"xiaofan","xiaoma","xiaowu"}
	fmt.Println(studentInfo)
}
```



#### 类型断言

空接口可以存储任意类型的值，那我们如何获取其存储的具体数据呢？

接口值：一个接口的值（简称接口值）是由`一个具体类型`和`具体类型的值`两部分组成的。这两部分分别称为接口的`动态类型`和`动态值`。

<img src="https://www.liwenzhou.com/images/Go/interface/interface.png" alt="接口值图解" style="zoom:50%;" />

想要判断空接口中的值这个时候就可以使用类型断言，其语法格式：

```go
x.(T)
```

其中:

- x：表示类型为`interface{}`的变量
- T：表示断言`x`可能是的类型。

该语法返回两个参数，第一个参数是`x`转化为`T`类型后的变量，第二个值是一个布尔值，若为`true`则表示断言成功，为`false`则表示断言失败。

```go
func main(){	
	var x interface{}
	x="hello shahe"
	v,ok:=x.(string)
	if ok{
		fmt.Println(v)
	}else{
		fmt.Println("类型断言失败")
}
```


因为空接口可以存储任意类型值的特点，所以空接口在Go语言中的使用十分广泛。

关于接口需要注意的是，只有当有两个或两个以上的具体类型必须以相同的方式进行处理时才需要定义接口。不要为了接口而写接口，那样只会增加不必要的抽象，导致不必要的运行时损耗。

### 单元测试



