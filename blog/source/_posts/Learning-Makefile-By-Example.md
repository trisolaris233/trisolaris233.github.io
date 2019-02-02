---
title: 通过一个小项目学习Makefile
date: 2019-02-02 13:42:48
tags:
- makefile
---

最近在linux上写了一个小项目，接触了make等工具，这次就通过一些辅助例子和我这个小项目来介绍一下makefile的基本写法。

## makefile何为物
> 在软件开发中，make是一个工具程序（Utility software），经由读取叫做“makefile”的文件，自动化建构软件。

这是来自维基百科的介绍， 其实说直白一点，就是一个编译工具。因此， 我们下载下来一个项目后， 一般会附带一个`makefile`文件。 更普遍的， 是附带一个`configure`文件，用于检查并生成需要的`makefile`文件，然后通过命令行执行`make`来编译。 执行`make install`来安装。

##  makefile的用途

考虑下面的文件目录：
```
.
└── src
    └── main.cpp
```
如果我们位于当前目录，要编译`main.cpp`实在是再简单不过，于是我们张口就莱: `g++ src/main.cpp -o main`。　这样就成功编译了`main.cpp`文件。显然这样敲命令不会比使用`makefile`麻烦多少，然而这是还没到`makefile`大显身手的时候。

## makefile的基本写法
当然，我们的目的是学习`makefile`，那么就以编译上面的`main.cpp`为例，介绍一下`makefile`的基本写法吧！

```makefile
# 注释
target(目标): dependencies(依赖文件)
<tab>command1 # 命令1
<tab>command2 # 命令2
```
一下子看不懂不要紧，且听我道来。`target`通常是我们要生成的文件的名字， `dependencies`是生成`target`所需的文件名列表， 如果为空就不需要写， 有多个文件用空格隔开。因此， 要把`main.cpp`编译成可执行文件`main`可以写成这样：`main: main.cpp`。

写好了`target`和`dependencies`后是换行， 接下来就要告诉`makefile`这个文件是如何生成出来的了。因此要键入编译指令。此处要注意`<tab>`是指一个`tab`键而不是四个空格， 这跟`python`有点像，熟悉`python`的小伙伴应该对此比较满意233。 因此`command1`和`command2`都是指命令行命令。

通过上面的介绍， 我们可以写出下面的makefile文件：
```makefile
main: src/main.cpp
	g++ src/main.cpp -o main
```
保存后， 进入控制台执行`make`, `make`就会自动的执行`makefile`文件中的第一个`target`。 


## 一个稍复杂的例子

考虑下面的文件目录：
```
.
└── src
    ├── a.cpp
    ├── a.h
    ├── b.cpp
    ├── b.h
    ├── c.cpp
    ├── c.h
    ├── d.cpp
    ├── d.h
    └── main.cpp

```
这是一个常见的小工程项目，其中`main.cpp`是主函数所在的源文件，`main.cpp`包含了`a.h`,`b.h`,`c.h`,`d.h`四个头文件，而四个头文件的功能实现分别放在对应的源文件中，先考虑如何用控制台命令编译这个项目？

很快我们想到了，于是章口就莱：
`g++ src/main.cpp src/a.cpp src/b.cpp src/d.cpp -o main`

很显然4个源文件还是可以用控制台命令轻松处理， 但对于这行命令我要提出两个问题：

1. 如果我们只修改了`b.cpp` 上面的命令会导致所有源文件都重新编译。
2. 4个源文件只是个例子，在一般规模的项目中，几十个源文件是司空见惯的。

对于第一个问题很容易解决，只要加上`-c`选项就行， 表示生成目标文件代码(`.o文件`)；对于第二个问题最简单的解决方法就是编写一个`makefile`来控制编译。

于是乎， 问题变成了：如何编写这个项目的`makefile`代码？

根据上面的例子， 可以先考虑下我预先写完的`makefile`文件：

```makefile
main: src/main.cpp src/a.o src/b.o src/c.o src/d.o
	g++ src/main.cpp src/a.o src/b.o src/c.o src/d.o -o myhttpd

src/a.o: src/a.cpp
	g++ -c src/a.cpp
	
src/b.o:
src/c.o:
src/d.o:
```

首先第一行给出了编译`main`的方法， 它依赖于`src/main.cpp`和后面的一大堆`.o`文件。那么这些`.o`文件是哪里来的呢？  下面的代码就在说明各个.o文件是怎么出来的。 这里我用了`makefile`比较良心的智能推断。 我只写了`src/a.o`依赖于`src/a.cpp`， 接下来的几个用不着我细说`makefile`就明白并且在执行时会自动编译。

接下来又出现了问题，这样编译会导致目录下有很多`.o`文件，如果哪一天我不需要了怎么清除他们呢？ 这个用命令行一样好办， `makefile`也可以帮忙解决这个问题。 我们只消在原来的文件中加上：
```makefile
clean:
	rm src/*.o
```
然后执行`make clean`就可以啦！

通过上面的例子我们知道`makefile`的`target`不是仅仅文件而已， 解释成`任务`或许更好。这样可以帮助我们对`makefile`的理解。`makefile`默认只执行第一个`target`和其依赖的文件。

## 改进makefile

考虑这样一个情景： 你写了一个项目， 也编写了`makefile`，但你发现`g++`简直是傻逼！ 你决定换成`clang`编译器， 这该如何是好？ 

你灵稽一动， 想到可以用一般文本编辑器都带的文本替换功能， 把所有`g++`换成`clang`就可以了， 这显然是个挺好的解决方案， 不过我们可以改进`makefile`文件的写法，就可以用一种更加优雅的方式解决这个问题（程序员总是强调**`优雅`**这个问题）

我们可以为`makefile`设置变量来改， `makefile`中变量的设置和其他语言不无二致。 举个例子：
```makefile
CC = g++

main: src/main.cpp
	$(CC) src/main.cpp -o main # $(CC)就是g++
```
这就是`makefile`中变量的设定。 是不是很简单？ 如果有多个， 用空格隔开就行， 调用的时候也是一样的。 这样一来， 我们就可以改写我们上面的`makefile`了！

```makefile
CC = g++
CFLAGS = -std=c++11	# 设置编译选项变量 表示启用c++11标准
OBJS = src/a.o src/b.o src/c.o src/d.o	# 设置目标代码文件变量

main: src/main.cpp $(OBJS)
	$(CC) $(CFLAGS) src/main.cpp $(OBJS) -o main

src/a.o: src/a.cpp
	g++ -c src/a.cpp
	
src/b.o:
src/c.o:
src/d.o:

clean:	# 清除.o文件
	rm src/*.o
	
install:	# 使用cp命令安装到可执行文件目录
	sudo cp main /usr/bin/main
```
这样一来， 我们就可以执行`make`来编译。 `make clean`来删除不必要的文件， `make install`来安装软件。 如果需要更换编器的画就把`CC = g++`换成`CC = clang`就可以了！

## 例子: myhttpd项目
学习了上面`makefile`的基本写法之后， 来观察一下[`myhttpd`](https://github.com/trisolaris233/myhttpd)项目的文件目录：
```
.
├── LICENSE
├── makefile
├── README.md
└── src
    ├── compress.cpp
    ├── compress.h
    ├── myhttpd.cpp
    ├── parse.cpp
    ├── parse.h
    ├── request.cpp
    ├── request.h
    ├── response.cpp
    ├── response.h
    ├── server.cpp
    ├── server.h
    ├── socket.cpp
    ├── socket.h
    ├── tools.cpp
    ├── tools.h
    └── welcome
        ├── index.html
        └── myhttpd.png
```

编译这个项目的`makefile`文件如下：
```makefile
CC = g++
CFLAGS = -std=c++11 -lz # 启用C++11， 链接zlib
OBJS = src/parse.o src/request.o src/response.o src/socket.o src/tools.o src/compress.o src/server.o

myhttpd: src/myhttpd.cpp $(OBJS)
	$(CC) $(CFLAGS) src/myhttpd.cpp $(OBJS) -o myhttpd

src/parse.o: src/parse.cpp
src/request.o:
src/response.o:
src/socket.o:
src/tools.o:
src/compress.o:
src/server.o:

clean:
	rm src/*.o

install:
	sudo cp ./myhttpd /usr/bin/myhttpd
```

相信通过上面的学习可以轻易的理解这段`makefile`代码， 而且我们发现通过`makefile`代码可以更加容易的观察各个文件的依赖关系。

## 后记
`makefile`是学习编程中或多或少会接触到的东西，今天通过一些实例简单的介绍了一下`make`这个使用的工具，希望能对大家有所帮助。

本篇博文只是介绍了`makefile`的冰山一角， 本人也在学习的路上。




