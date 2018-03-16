---
layout:		post
title:		"（译）The Boost C++ Libraries - 概览"
subtitle: 	"\"Boost as a Weapon\""
date:		2018-03-15 10:58:00
author:		"Darjun"
header-img:	"img/post-bg-2015.jpg"
tags:
    - boost
---

### 概览

现在已经有100多个Boost库了。本书详细讨论下面的库：

| 表 1. 将要讨论的库 |
| Boost库 | 标准 | 简要描述 |
| ------- | ---- | -------- |
| Boost.Accumulators | | Boost.Accumulators提供累加器，可以收集数据来计算例如平均值、标准差等。 |
| Boost.Algorithm | | Boost.Algorithm提供多个算法，补充标准库中的算法。 |
| Boost.Any | | Boost.Any提供一个叫做`boost.any`的类型，可以用来存放任意类型的对象。 |
| Boost.Array | TR1,C++11 | Boost.Array使得可以像处理标准库中的容器一样处理C++数组。 |
| Boost.Asio | | Boost.Asio允许你开发异步处理数据的应用，例如网络应用。 |
| Boost.Assign | | Boost.Assign提供帮助函数向容器中添加多个值，而不用重复调用像`push_back()`之类的成员函数。 |
| Boost.Atomic | C++11 | Boost.Atomic定义了类`boost::atomic`可以在数值类型上执行原子操作。该库用在需要在线程间共享数值的多线程程序中。 |
| Boost.Bimap | | Boost.Bimap提供一个叫做`boost::bimap`的类，其类似于`std::map`。关键的不同是`boost::bimap`允许你既可以使用键，又可以使用值来查找。 |
| Boost.Bind | TR1,C++11 | Boost.Bind是一个适配器。可以传递一个函数作为模板参数，即使这个函数的签名与期待的模板参数不兼容。 |
| Boost.Chrono | C++11 | Boost.Chrono定义大量时钟来获取当前时间或CPU时间等值。|
| Boost.CircularBuffer | | Boost.CircularBuffer提供一个常量内存大小的循环容器。 |
| Boost.CompressedPair | | Boost.CompressedPair提供数据结构`boost::compressed_pair`，该结构与`std::pair`类似。但是，如果模板参数为空类，该结构占用更少的内存。 |
| Boost.Container | | Boost.Container定义了标准库中的所有容器类，还有其他的容器例如`boost::container::slist`。 |
| Boost.Conversion | | Boost.Conversion提供两个转型操作符，来执行向下转型（downcast）和交叉转型（crosscast）。 |
| Boost.Coroutine | | Boost.Coroutine使得可以在C++中使用协程。在其他程序设计语言中，协程通常用`yield`关键字使用。 |
| Boost.DateTime | | Boost.DateTime可被用于处理、读写日期和时间值。 |
| Boost.DynamicBitset | | Boost.DynamicBitset提供一个类似`std::biset`的结构，但是它是运行期配置的。 |
| Boost.EnableIf | C++11 | Boost.EnableIf可以基于类型属性来重载函数。 |
| Boost.Exception | | Boost.Exception运行你给抛出的异常添加额外的数据，因此可以给`catch`处理器提供更多的数据。 |
| Boost.FileSystem | | Boost.FileSystem提供一个用来处理路径的类和几个访问文件和目录的函数。 |
| Boost.Flyweight | | Boost.Flyweight使得更容易地使用同名的设计模式。 |
| Boost.Foreach | | Boost.Foreach提供了一个宏，类似于C++11引入的for-range循环。 |
| Boost.Format | | Boost.Format使用一个类型安全的，可扩展的类`boost:format`来代替`std::printf`。 |
| Boost.Function | TR1,C++11 | Boost.Function简化函数指针的定义。 |
| Boost.Fusion | | Boost.Fusion允许你创建异质的容易——存储不同类型元素的容器。 |
| Boost.Graph | | Boost.Graph提供类似在一个图中寻找两个点之间最短路径的算法。 |
| Boost.Heap | | Boost.Heap提供标准库中类`std::priority_queue`的很多变体。 |
| Boost.Integer | C++ | Boost.Integer为整型定义了一些特殊的类型，这些自C99在1999年发布之后C程序员就可使用了。 |
| Boost.Interprocess | | Boost.Interprocess使用共享内存帮助应用快速高效地通信。 |
| Boost.Intrusive | | Boost.Intrusive提供比标准库容器拥有更高性能的容器，但是对它们包含的对象有特殊要求。 |
| Boost.IOStreams | | Boost.IOStreams提供流和过滤器。过滤器可以与一个流连接，例如写压缩的数据。 |
| Boost.Lambda | | Boost.Lambda允许你不用C++11就能定义匿名函数。 |
| Boost.LexicalCast | | Boost.LexicalCast提供一个转型操作符来将数字转为字符串和相反操作。 |
| Boost.Lockfree | | Boost.Lockfree定义线程安全容器，多个线程可以并发地访问。 |
| Boost.Log | | Boost.Log是Boost中的日志库。 |
| Boost.MetaStateMachine | | Boost.MetaStateMachine使之可以像定义在UML中一样开发状态机。 |
| Boost.MinMax | C++11 | Boost.MinMax提供在一个容器中查找最小和最大值的算法，不必调用`std::min()`和`std::max()`。 |
| Boost.MPI | | Boost.MPI为MPI标准提供一个C++接口。 |
| Boost.MultiArray | | Boost.MultiArray简化多维数组的使用。 |
| Boost.MultiIndex | | Boost.MultiIndex允许你定义新的容器，它能支持多个接口，例如源自`std::vector`和`std::map`的接口。 |
| Boost.NumericConversion | | Boost.NumericConversion提供一个转型操作，可以安全地在不同数值类型中转换，而不产生溢出状态。 |
| Boost.Operators | | Boost.Operators允许许多操作符被自动重载，基于已经定义的操作符。 |
| Boost.Optional | | Boost.Optional提供一个类来产生可选返回值。不总是返回结果的函数不用再使用-1或null之类的值了。 |
| Boost.Parameter | | Boost.Parameter让你以键/值对形式给函数传递参数，像Python之类的程序语言一样。 |
| Boost.Phoenix | | Boost.Phoenix可以不使用C++11创建lambda函数。与C++11的lambda函数不同，该库定义的lambda函数可以是通用的。 |
| Boost.PointerContainer | | Boost.PointerConatiner提供针对管理动态分配对象优化过的容器。 |
| Boost.Pool | | Boost.Pool是一个管理内存的库。例如，Boost.Pool定义了一个分配器，针对需要创建和销毁很多同样大小的对象做过优化。 |
| Boost.ProgramOptions | | Boost.ProgramOptions允许应用去定义和计算命令行选项。 |
| Boost.PropertyTree | | Boost.PropertyTree提供了在一个类似树（tree-like）的结构中存储键/值对的容器。 |
| Boost.Random | TR1,C++11 | Boost.Random提供了随机数生成器。 |
| Boost.Range | | Boost.Range引入了一个叫做范围（range）的概念，代替通常在容器上调用`begin()`和`end()`获取的迭代器。范围使得我们不用给算法传递一对迭代器。 |
| Boost.Ref | TR1,C++11 | Boost.Ref提供了适配器，允许传递不可拷贝的对象的引用给拷贝传参的函数。 |
| Boost.Regex | TR1,C++11 | Boost.Regex提供了使用正则表达式搜索字符串的功能。 |
| Boost.ScopeExit | | Boost.ScopeExit提供了一些宏来定义当前作用域结束时调用的代码块。可以使用这种方式在当前作用域结束时释放资源，而不使用智能指针或其他类。 |
| Boost.Serialization | | Boost.Serialization可以序列化对象并将它存储到文件中，以便以后重新加入。 |
| Boost.Signals2 | | Boost.Signals2是一个基于信号槽概念的事件处理框架。它将函数与信号相关联，当信号触发时，自动调用相应的函数。 |
| Boost.SmartPointers | TR1, C++11(部分) | Boost.SmartPointer提供了一组智能指针，简化了对自动分配对象的管理。 |
| Boost.Spirit | | Boost.Spirit可以使用类似EBNF（Extended Backus-Naur-Form）的语法生成解析器。 |
| Boost.StringAlgorithms | | Boost.StringAlgorithms提供了许多单独的函数来方便字符串的操作。 |
| Boost.Swap | | Boost.Swap定义了`boost:swap()`，与`std::swap()`功能相同，但是对许多Boost库做过优化。 |
| Boost.System | C++11 | Boost.System提供了一个框架来处理系统和应用特定的错误码。 |
| Boost.Thread | C++11 | Boost.Thread可以开发多线程程序。 |
| Boost.Timer | | Boost.Timer定义了可以测量代码性能的时钟。 |
| Boost.Tokenizer | | Boost.Tokenizer允许你迭代字符串中的标识。 |
| Boost.Tribool | | Boost.Tribool提供一个类型。与`bool`不同，它区分三种状态，而不是两种。 |
| Boost.Tuple | TR1,C++11 | Boost.Tuple提供了一个泛化版本的`std::pair`，可以存储任意数量的值，不仅是两个。 |
| Boost.TypeTraits | TR1,C++11 | Boost.TypeTraits提供检查类型属性的功能。 |
| Boost.Unordered | TR1,C++11 | Boost.Unordered提供了两个散列容器：`boost::unordered_set`和`boost::unordered_map`。 |
| Boost.Utility | | Boost.Utility是多种工具的一个集合，它们太小了而无法拥有自己的库，也适合放入其他库中。 |
| Boost.Uuid | | Boost.Uuid定义了类`boost::uuids::uuid`和生成器来创建UUID。 |
| Boost.Variant | | Boost.Variant允许像`union`那样定义类型，组织多种类型。 |
| Boost.Xpressive | | Boost.Xpressive可以使用正则表达式来搜索字符串。正则表达式以C++代码的形式编码，而不是字符串。 |

想必，标准的下一个版本将会是C++14。有很多项目小组在为C++14的各个主题工作。这些活动被称为技术规格（TS）。例如，文件系统TS工作在基于Boost.FileSystem的标准扩展上以访问文件和目录。你可以在[isocpp.org][1]上找到更多C++14和C++标准化的信息。

[1]: https://isocpp.org/