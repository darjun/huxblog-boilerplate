---
layout:		post
title:		"（译）The Boost C++ Libraries - 安装"
subtitle: 	"\"Boost as a Weapon\""
date:		2018-03-15 10:02:00
author:		"Darjun"
header-img:	"img/post-bg-2015.jpg"
tags:
    - boost
---

### 安装

Boost库以源码形式发布。大部分库仅由一个头文件组成，可以直接使用。有一些库需要编译。为了使编译尽可能的简单，可以使用基于Boost.Build的一个自动化安装进程。Boost.Build不是验证和安装单个库，而是自动安装完整的库。Boost.Build可以在很多操作系统和编译器中使用，能基于适当的配置文件编译每个单独的库。

要使用Boost.Build来自动安装Boost库，要使用命令行程序**bjam**。Boost库带有该程序的源码，而不是可执行程序。因而，编译和安装Boost库需要两个步骤。下载Boost库后，切换到Boost目录，在命令行中输入下面命令：

1. 在Windows上输入`bootstrap`，其他比如Linux系统上输入`./bootstrap.sh`来编译**bjam**。该脚本自动查找C编译器来编译**bjam**。

2. 然后，在Windows上输入`bjam`，其他系统上输入`./bjam`来安装Boost库。

你只需要使用一次**bootstrap**来编译**bjam**。然而，你可能需要经常使用**bjam**，因为**bjam**支持以不同的方式编译Boost库的命令行选项。如果你不带任何命令行选项运行**bjam**，将会使用磨人的配置。因为默认配置并不总是适合的，所以你应当了解最重要的命令行选项：

* 命令行选项`stage`和`install`指定Boost库是安装在一个叫做`stage`的子目录还是系统层面可用。系统层面的含义与系统有关。在Windows系统中，目标目录是`C:\Boost`，在Linux上是`/usr/local`。也可以通过`--prefix`选项指定目标目录。不带命令行选项运行**bjam**意味着使用**stage**。

* 如果不带任何命令行选项运行**bjam**，它将查找一个合适的C++编译器。可以使用`--toolset`选项选择一个指定的编译器。为了在Windows上指定`Visual C++ 2013`，以`--toolset=msvc-12.0`选项运行**bjam**。为了在Linux上指定GCC编译器，使用`--toolset=gcc`。

* 命令行选项`--build-type`决定库以什么编译类型创建。默认地，这个选项被设置为`minimal`，意味着只有release版本会被创建。对于那些想要使用Visual C++或GCC为他们的项目创建debug版本的开发者来说，这可能是一个问题。因为这些编译器自动尝试去Boost库的debug版本，这可能会出现错误。这种情况下，需要设置`--build-type`选项为`complete`来同时生成Boost库的debug和release版本。这可能需要一段时间，正因如此`complete`不是默认选项。

* 在Windows中编译使用的Boost库文件名中含有版本号和多个其他标识。它们让我们可以辨别例如一个库是编译为debug还是release版本。`libboost_atomic-vs120-mt-gd-1_56`就是这样一个文件名。这个库是用Visual C++ 2013编译的。它属于1.56.0版本的Boost库。它是一个debug版本，并且可以用在多线程程序中。使用命令行参数`--layout`，**bjam**可以被指定生成其他文件名。例如，如果你设置该选项为`sytstem`，同一个文件将会是`liboost_atomic`。在Linux系统中，`system`是默认设置。如果你想要Linux中的文件名与Windows中默认生成的一样，设置`--layout`为`versioned`。

要使用Visual C++ 2013构建boost库的debug和release版本，并且将其安装在`D:\Boost`目录，输入以下命令：

```
bjam --toolset=msvc-12.0 --build-type=complete --prefix=D:\Boost install
```

要在Linux上构建并安装在默认目录，使用以下命令：
```
bjam --toolset=gcc --build-type=complete install
```

你还可以使用很多其他的命令行选项来详细地指定如何编译Boost库。观察以下命令：
```
bjam --toolset=msvc-12.0 debug release link=static runtime-link=shared install
```

`debug`和`release`选项使得`debug`和`release`版本都会被生成。`link=static`只会创建静态链接库。`runtime-link=shared`指定C++运行时库是动态链接的，这是Visual C++ 2013项目的默认设置。