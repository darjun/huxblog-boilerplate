---
layout:     post
title:      "深入理解CallStack&EventLoop"
subtitle:   "\"Javascript in depth\""
date:       2018-11-23 12:57:00 
author:     "Darjun"
header-img: "img/post-bg-2015.jpg"
tags:
    - javascript
---

+ [1.概述](#概述)
+ [2.Call Stack](#CallStack)
+ [6.参考链接](#参考链接)

<!-- ![CallStack & EventLoop](/img/in-post/callstack-eventloop/eventloop.jpeg) -->

### <span id="概述">1.概述</span>
众所周知，Javascript是一个单线程的编程语言。这意味着，在Javascript中，同一时间只能做一件事情。这样的设计有很多优势，例如简单，避免了多线程中复杂的状态同步，写程序时不用考虑并发访问。但同时也带来了一些其他问题，其中比较突出的一个问题是：代码逻辑不直观。由于Javascript是单线程的，其中只有一个执行序列。所以，在执行异步操作（例如定时，网络请求这些不能立即完成的操作）时，Javascript运行时不可能在那里等着操作完成。否则整个运行时都被阻塞在那里了，导致其他所有的操作都无法进行，例如网页渲染，用户点击、滚动页面等操作。这样的用户体验是非常糟糕的。正因为如此，Javascript引入了回调的机制。进行异步操作时传入一个回调，操作完成之后由Javascript运行时执行这个回调，将结果传入。慢慢地，Javascript中充斥着大量的回调。过多的使用回调让一段完整的逻辑被拆分成了很多片段，非常不利于阅读与维护。回调过多的问题在NodeJS中更为突出，故而出现了[`Promise`][1]（见我的前一篇博文）和`async/await`。

那么异步操作完成时，Javascript运行时是怎样感知到并调用对应的回调函数的呢？
答案是`EventLoop`（事件循环)。

要了解`EventLoop`是怎样运作的，我们首先需要了解Javascript是怎样处理一个个任务，调用一个个函数的，这就是`Call Stack`（调用栈）所做的事。

题外话：

在使用一门框架或语言时，对于是否需要了解底层运作机制和原理，往往会有比较大的争论。有人说，我不了解内部原理同样可以写出好程序，那为什么还需要花时间去研究呢？
对此，我觉得了解底层原理还是非常有必要的。有下面几个好处：

* 可以让我看到全貌，了解整个系统是如何运作的。

* 底层原理大多是相通的，例如几乎所有语言的函数调用底层都是利用`Call Stack`来实现的。学会了Javascript的`Call Stack`运作机制，在学习其他语言的相关概念时往往能事半功倍。

* 了解底层可以让我们心中有数，明白什么事情能做，什么事情不能做。例如Javascript是单线程的，我们写代码时一定不能让线程阻塞了:smile:。

* 了解底层可以让我们更好的优化代码。当程序性能出现瓶颈时，可以更快地定位问题。

### <span id="CallStack">2.Call Stack</span>
相信有过其他语言编程经验的读者都听说过`Call Stack`的概念。Javascript中的`Call Stack`类似。

`Call Stack`是一个栈结构，栈的特点是LIFO（后入先出），出栈入栈只会在一端（也就是栈顶）进行。`Call Stack`是用来处理函数调用与返回的。每次调用一个函数，Javascript运行时会生成一个新的调用结构压入`Call Stack`。而函数调用结束返回时，JavaScript运行时会将栈顶的调用结构弹出。由于栈的LIFO特性，每次弹出的必然是最新调用的那个函数的结构。Javascript启动时，从文件或标准输入加载程序。加载完成时，Javascript运行时会生成一个匿名的函数，函数体就是来自输入的代码。这个函数就有点类似于C/C++中的`main()`函数，是我们的入口函数。我们姑且称之为`<main>`函数。Javascript启动时，首先调用就是`<main>`函数。看下面代码：

```
function func1() {
    console.log('in function1');
}

function func2() {
    func1();
    console.log('in function2');
}

function func3() {
    func2();
    console.log('in function3');
}

func3();
```

上面代码很好理解，我们来看看Javascript是如何运行这段代码的。Javascript首先加载代码，创建一个匿名`<main>`包裹这段代码并调用该函数。调用函数时创建一个新的调用结构压入`Call Stack`。`<main>`函数执行，依次定义函数`func1`、`func2`、`func3`，然后调用函数`func3`。为`func3`创建调用结构并压栈。函数`func3`中调用`func2`，为`func2`创建调用结构并压栈。函数`func2`中调用`func1`，为`func1`创建调用结构并压栈。这个过程中，`Call Stack`的变化如下。

![Call Stack Push](/img/in-post/callstack-eventloop/callstack-push.png)

然后，函数`func1`执行完成，从栈顶弹出调用结构。然后`func2`继续执行，`func2`执行完成后从栈顶弹出其调用结构。然后`func3`继续执行，`func3`执行完成后从栈顶弹出其调用结构。这个过程中，`Call Stack`的变化如下。

![Call Stack Pop](/img/in-post/callstack-eventloop/callstack-pop.png)

当然，我这里有一个地方不太严谨。不知道读者有没有注意到，`console.log`也是函数函数哦。所以在`func1`中调用`console.log`时，`Call Stack`上也会有对应的调用堆栈。`func2`，`func3`中的`console.log`调用同样如此。有兴趣的话，可以自己画一画完整的调用流程，这样可以加深理解:smile:。

这里我推荐大家使用Google Chrome的开发者工具来帮助我们理解`Call Stack`。下图是上面代码在开发者工具中的一步步执行的结果：

![Call Stack Demo](/img/in-post/callstack-eventloop/callstack-demo.gif)

* Chrome中`<main>`称为`<anonymous>`。

* 单步执行时，重点观察右侧工具栏中**Call Stack**一栏的变化。


### <span id="参考链接">6.参考链接</span>

[1]: https://darjun.github.io/2018/11/08/javascript-promise-intro/