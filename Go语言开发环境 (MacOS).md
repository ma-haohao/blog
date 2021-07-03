# Go语言开发环境 (MacOS)

### 1. 安装Go语言及搭建Go语言开发环境

#### 下载

下载地址: Go官网下载地址：https://golang.org/dl/

<img src="https://www.liwenzhou.com/images/Go/install_go_dev/download1.png" alt="download1" style="zoom: 67%;" />

#### 安装

下载可执行文件版，直接点击**下一步**安装即可，默认会将go安装到`/usr/local/go`目录下。

![Mac安装Go](https://www.liwenzhou.com/images/Go/install_go_dev/mac_install_go.png)

#### GOROOT和GOPATH (无需再自己配置)

`GOROOT`和`GOPATH`都是环境变量，其中`GOROOT`是我们安装go开发包的路径，而从Go 1.8版本开始，Go开发包在安装完成后会为`GOPATH`设置一个默认目录，并且在Go1.14及之后的版本中启用了Go Module模式之后，不一定非要将代码写到GOPATH目录下，所以也就**不需要我们再自己配置GOPATH**了，使用默认的即可。

**GOPROXY 非常重要**

Go1.14版本之后，都推荐使用`go mod`模式来管理依赖环境了，也不再强制我们把代码必须写在`GOPATH`下面的src目录了，你可以在你电脑的任意位置编写go代码。（网上有些教程适用于1.11版本之前。）

默认GoPROXY配置是：`GOPROXY=https://proxy.golang.org,direct`，由于国内访问不到`https://proxy.golang.org`，所以我们需要换一个PROXY，这里推荐使用`https://goproxy.io`或`https://goproxy.cn`。

可以执行下面的命令修改GOPROXY：

```bash
go env -w GOPROXY=https://goproxy.io,direct
```

### 2. Go开发环境 (VS Code)

Go采用的是UTF-8编码的文本文件存放源代码，理论上使用任何一款文本编辑器都可以做Go语言开发，这里推荐使用`VS Code`和`Goland`。 `VS Code`是微软开源的编辑器，而`Goland`是jetbrains出品的付费IDE。

我们这里使用`VS Code` 加插件做为go语言的开发工具。

#### 下载与安装

`VS Code`官方下载地址：https://code.visualstudio.com/Download

### 3. Go语言编译运行实例

#### 第一个Go程序

```shell
//在桌面创建一个hello目录
cd Desktop/
mkdir hello
cd hello/
//mod 初始化
go mod init hello
```

编写代码

```go
package main  // 声明 main 包，表明当前是一个可执行程序

import "fmt"  // 导入内置 fmt 包

func main(){  // main函数，是程序执行的入口
	fmt.Println("Hello World!")  // 在终端打印 Hello World!
}
```

**非常重要！！！** 如果此时VS Code右下角弹出提示让你安装插件，务必点 **install all** 进行安装。

#### 运行方法

(1). 直接用vscode run模块试运行(只有单个执行文件时可以使用该方法)

<img src="/Users/mhy/Library/Application Support/typora-user-images/image-20210623113131548.png" alt="image-20210623113131548" style="zoom:67%;" />

(2). go run main.go

(3). go build 然后在终端中运行生成的编译完成的文件

**重要: go run main.go 与go run *.go的区别**

`go run main.go`只会编译运行指定文件，如果你是多个文件就需要使用 `go run *.go`才会编译当前目录下的所有go文件。

例子：

```go
文件目录形式
hello
├── add.go
├── go.mod
└── main.go
```

```go
//add file:
package main

import "fmt"

func Add(i,j int) int{
	fmt.Println("Add Test")
	return i+j
}

//main file: 
package main

import "fmt"

func main(){
	ret:=Add(1,2)
	fmt.Println(ret)
}
```

如果运行go run main.go会报以下错误: 

command-line-arguments
./main.go:6:7: undefined: Add

此处应运行go run *.go, 这样就会联合编译位于hello目录下的所有包

```go
//output
Add Test
3
```

### 4. mod 包管理

#### package(包): 多个Go源码的集合

1. package不限于一个文件，可以由多个文件组成。
2. 不要求package的名称和所在目录名相同，但是最好保持相同，否则容易引起歧义。
3. 每一个子目录中只能存在一个package，否则编译时会报错。
4. package是以绝对路径GOPATH来寻址，不要使用相对路径来import

#### 包管理工具： go mod

命令1. go mod init 项目名称  (注: 只需要在根目录下创建即可)

公有/私有变量：通过首字母的大小写来定义标识符是否对外可见

#### go mod常用操作命令

go mod 有以下命令：

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| download | download modules to local cache(下载依赖包)                  |
| edit     | edit go.mod from tools or scripts（编辑go.mod)               |
| graph    | print module requirement graph (打印模块依赖图)              |
| verify   | initialize new module in current directory（在当前目录初始化mod） |
| tidy     | add missing and remove unused modules(拉取缺少的模块，移除不用的模块) |
| vendor   | make vendored copy of dependencies(将依赖复制到vendor下)     |
| verify   | verify dependencies have expected content (验证依赖是否正确） |
| why      | explain why packages or modules are needed(解释为什么需要依赖) |

比较常用的是 `init`,`tidy`,`edit`

### 3. 使用go mod管理一个新项目

#### 1. 初始化项目

可以随便找一个目录创建项目

```bash
mkdir Gone
cd Gone
go mod init Gone
```

查看一下 go.mod文件

```go
module Gone

go 1.16
```

go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。

go.mod 提供了module, require、replace和exclude 四个命令

- `module` 语句指定包的名字（路径）
- `require` 语句指定的依赖项模块
- `replace` 语句可以替换依赖项模块
- `exclude` 语句可以忽略依赖项模块

#### 2. 添加依赖

创建 main.go文件

```go
package main

import (
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "pong",
        })
    })
    r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```

