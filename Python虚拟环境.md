

# Python虚拟环境

## 1. 概述

### 什么是虚拟环境？

其实就是一个文件夹。其为一个特定版本的python与对应的一些版本的包的集合。

### 为什么要使用虚拟环境? (virtualenv)

实际中可能会存在以下几个场景：

1. 某个项目是用低版本的python开发的，这与本身机器中所安装的python不兼容。如果没有虚拟环境，我们则需要先把自己电脑中的python卸载了，然后安装旧版本的python。结束后还需要反向操作把python的版本升回去。而使用虚拟环境则可以保证我们在一台机子中能够有多个版本的python共存。
2. 两个项目虽然用了同样名称的包，但是开发过程中使用的包的版本不同，这样可能会导致新老版本的代码不兼容。而使用虚拟环境则可以解决这一问题。我们只要在开发某个项目中，选择和它所匹配的虚拟环境。这样我们就不需要来回倒腾不同的版本。两个项目也可以在电脑中共存。
3. 在项目迁移过程中，如果有虚拟环境，我们可以连带虚拟环境一起迁移到其它电脑上，这样就可以完美地解决不同电脑中包不兼容的问题。
4. ...

可以看出，虚拟环境可以看成一个与全局环境相隔离的局部环境。而相比于全局环境而言，这个局部环境更容易控制，我们可以把这个环境控制成完全匹配我们所开发的项目的样式。这样更有利于我们对这个项目的依赖进行控制以及后续项目的迁移。

### 主要内容

具体而言，我们需要解决以下几个问题：

1. 如何去创建一个虚拟环境？

2. 如何用创建的这个虚拟环境去开发相关的程序？

3. 如何将虚拟环境与程序代码进行打包迁移？

    

## 2. 虚拟环境的创建

使用的依赖包为：virtualenv

```python
pip install virtualenv
```

我们以virtualTest项目为例，这里我们假设整个virtualTest所用的依赖包的版本与原本全局情况下的包都不同，这种情况下我们需要单独为virtualTest建立一个虚拟环境。

1. 建立virtualTest项目的目录，并将虚拟环境放在这个目录中


```shell
#创建用于存放虚拟环境的文件夹
mkdir virtualTest
cd virtualTest
#创建虚拟环境
virtualenv venv #[这里venv可以更换为任意项目名称]
```

这样我们就在virtualTest目录下生成了一个名为venv的虚拟环境。

<img src="/Users/mhy/Library/Application Support/typora-user-images/image-20210624102828785.png" alt="image-20210624102828785" style="zoom:50%;" />

查看venv目录树：

```shell
venv
├── bin
│   ├── activate
│   ├── activate.csh
│   ├── activate.fish
│   ├── activate.ps1
│   ├── activate.xsh
│   ├── activate_this.py
│   ├── pip
│   ├── pip-3.8
│   ├── pip3
│   ├── pip3.8
│   ├── python -> /opt/anaconda3/bin/python
│   ├── python3 -> python
│   ├── python3.8 -> python
│   ├── wheel
│   ├── wheel-3.8
│   ├── wheel3
│   └── wheel3.8
├── lib
│   └── python3.8
│       └── site-packages
│           ├── __pycache__
│           ├── _distutils_hack
│           ├── _virtualenv.pth
│           ├── _virtualenv.py
│           ├── distutils-precedence.pth
│           ├── pip
│           ├── pip-21.1.1.dist-info
│           ├── pip-21.1.1.virtualenv
│           ├── pkg_resources
│           ├── setuptools
│           ├── setuptools-56.0.0.dist-info
│           ├── setuptools-56.0.0.virtualenv
│           ├── wheel
│           ├── wheel-0.36.2.dist-info
│           └── wheel-0.36.2.virtualenv
└── pyvenv.cfg
```

其中bin目录下放的该虚拟环境锁用的python，而lib目录下则放的是python的依赖包。

可以看出通过该方法分离出来的虚拟环境是纯净的，即只包含了pip等几个本身自带的依赖包。这样也方便我们后续进行迁移打包时，打入其它一些我们不需要的依赖使得整个文件变得臃肿。

在后续只要我们激活这个虚拟环境后，则所有的开发（包括依赖的安装等）都是基于这个虚拟环境展开的。

## 3. 利用虚拟环境来开发程序

### 激活虚拟环境

首选进入venv目录下，然后使用

```shell
source ./bin/activate
```

这样就可以开启虚拟环境了，开启后我们可以看到

<img src="/Users/mhy/Library/Application Support/typora-user-images/image-20210624104340251.png" alt="image-20210624104340251" style="zoom:67%;" />

在目录的最开始有一个（venv），这个说明了我们目前所在的虚拟环境。此时我们之后所有的操作都是基于这个虚拟环境进行的。例如我们想要安装numpy这个包，则会安装到venv/lib这个目录下。·

```shell
#在venv虚拟环境下
pip install numpy
#查看目前环境下安装的依赖包
pip list
#显示结果
Package    Version
---------- -------
numpy      1.21.0
pip        21.1.1
setuptools 56.0.0
wheel      0.36.2
```

可以看出在numpy已经安装到了虚拟环境下了

### 退出虚拟环境

```shell
deactivate
```

## 4. 虚拟环境的迁移

#### 方法一：直接打包

如果项目和虚拟环境在同一个目录下，可以连同项目加虚拟环境一起打包。然后在其它机器下进行解压，并激活虚拟环境。这样就能使得整个程序加依赖都实现完美迁移了。

#### 方法二：使用pip命令

大多数情况下我们的虚拟环境和项目本身是不在同一个文件夹下的，这时候我们就可以使用pip命令

```shell
#在项目目录下使用，同时保证开发用的虚拟环境处于激活状态
pip freeze>requirements.txt
```

这样该项目中所使用过的包都会被保存到名为requirements.txt的文件中（包括了包名加所使用的版本号信息）

```shell
#requirements.txt中的信息

APScheduler==3.7.0
Django==2.2.5
django-apscheduler==0.5.1
django-cors-headers==3.6.0
django-cors-middleware==1.5.0
django-redis==4.12.1
djangorestframework==3.12.2
djangorestframework-jwt==1.11.0
psycopg2==2.8.6
PyJWT==1.7.1
pytz==2021.1
redis==3.5.3
six==1.15.0
sqlparse==0.4.1
tzlocal==2.1
```

在做项目迁移时，我们只需要把该文件同项目一起放到新的机器上，然后使用以下命令：

```shell
pip install -r requirements.txt
```

这样在新的机器下就会安装requirements.txt中所有对应版本的包，从而保证新环境下代码的兼容性。

**当然得保证新机器所用的python版本与开发所用的是一致的。**

## 5. 小结

简而言之，整个虚拟环境的使用可以参考下图：

![image-20210624112748126](/Users/mhy/Library/Application Support/typora-user-images/image-20210624112748126.png)

1. 在原本的python版本下，我们可以使用virtualenv来创建虚拟环境，仅把原本的python本体给提取出来，创建一个纯净的局部环境。（当然创建时也可以不用原本pyhton版本，而是自己指定特别的版本）

2. 之后对于不同的项目，我们可以在不同的虚拟环境下进行开发，同时针对不同的项目需求安装不同类型和版本的依赖包。
3. 可以使用直接打包或者pip的方式进行项目的迁移。保证代码在新的环境下能够正常运行。
