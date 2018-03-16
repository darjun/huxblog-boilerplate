---
layout:		post
title:		"（译）The Boost C++ Libraries - 第一部分"
subtitle: 	"\"Boost as a Weapon\""
date:		2018-03-16 15:40:00
author:		"Darjun"
header-img:	"img/post-bg-2015.jpg"
tags:
    - boost
---

### 目录：
1. 唯一所有权
2. 共享所有权
3. 特殊的智能指针

[Boost.SmartPointers][1]库提供了多种智能指针。它们帮你管理动态分配的对象。在智能指针中就是在析构函数中释放动态分配的对象。因为当智能指针的作用域结束时，它的析构函数会执行。所以，动态分配对象的释放是可以保证的。不会再有例如你忘记调用`delete`而出现的内存泄漏了。

自C++98，标准库已经包含智能指针`std::auto_ptr`。但是，自C++11之后，`std::auto_ptr`已经被废弃了。C++11中，标准库中引入了新的更好的智能指针。`std::shared_ptr`和`std::weak_ptr`源自Boost.SmartPointers，在该库中叫做`boost:shared_ptr`和`boost::weak_ptr`。没有`std::unique_ptr`的对照物。但是，Boost.SmartPointers提供了四种其他的智能指针——`boost::scoped_ptr`，`boost::scoped_array`，`boost::shared_array`和`boost::intrusive_ptr`。这些标准库中都没有。

### 唯一所有权

`boost::scoped_ptr`是一个智能指针，它是一个动态分配对象的唯一拥有者。`std::scoped_ptr`不能被拷贝和移动。该智能指针定义在头文件`boost/scoped_ptr.hpp`中。

例1.1 使用`boost::scoped_ptr`
```
#include <boost/scoped_ptr.hpp>
#include <iostream>

int main()
{
	boost::scoped_ptr<int> p{new int{1}};
	std::cout << *p << '\n';
	p.reset(new int{2});
	std::cout << *p.get() << '\n';
	p.reset();
	std::cout << std::boolalpha << static_cast<bool>(p) << '\n';
}
```

一个`boost::scoped_ptr`类型的智能指针不能移交一个对象的所有权。一旦以一个地址初始化，该动态分配的对象会在析构函数执行或成员函数`reset()`被调用时释放。

例1.1使用一个类型为`boost::scoped_ptr<int>`的智能指针**p**。**p**以一个指向动态分配对象的指针初始化，该对象存储数字1。通过`operator*`，**p**被解引用，`1`被写到标准输出。

可以使用`reset()`将一个新的地址存入智能指针。示例就是通过这种方式，将一个新分配的包含数字`2`的`int`对象传给**p**。调用`reset()`，当前**p**中引用的对象会被自动销毁。

`get()`返回智能指针中存储的对象的地址。示例解引用了`get()`返回的地址，并且标准输出中写入了`2`。

`boost:scoped_ptr`重载了`operator bool`操作符。如果智能指针包含一个对象的引用，也就是说它不为空，`operator bool`返回`true`。示例在标准输出写入`false`，因为**p**调用`reset()`重置了。

`boost::scoped_ptr`的析构函数使用`delete`释放引用的对象。这就是为什么`boost::scoped_ptr`一定不能用一个动态分配的数组初始化。动态分配的数组必须用`delete[]`释放。对于数组，Boost.SmartPointers提供了`boost::scoped_array`类。

例1.2 使用`boost::scoped_array`
```
#include <boost/scoped_array.hpp>

int main()
{
	boost::scoped_array<int> p{new int[2]};
	*p.get() = 1;
	p[1] = 2;
	p.reset(new int[3]);
}
```

使用智能指针`boost:scoped_array`与`boost::scoped_ptr`相似。关键的不同点是，`boost::scoped_array`的析构函数使用`delete[]`操作符来释放包含的对象。因为这个操作符只适用于数组，`boost::scoped_ptr`必须使用一个动态分配的数组地址来初始化。

`boost::scoped_array`定义在`boost/scoped_array.hpp`。

`boost::scoped_array`提供了`operator[]`和`operator bool`的重载。使用`operator[]`可以访问数组的一个特定元素。因此，一个`boost::scoped_array`类型的对象行为上有它持有的数组相似。例1.2将数字2保存在由**p**引用的数组的第二个元素中。

像`boost::scoped_ptr`一样，`boost::scoped_array`提供成员函数`get()`和`reset()`来获取和重新初始化包含对象的地址。

### 共享所有权

智能指针`boost::shared_ptr`与`boost::scoped_ptr`相似。关键的不同是`boost::shared_ptr`对象的独占所有者。所有权能与其他`boost::shared_ptr`类型的智能指针共享。在这种情形下，直到引用该对象的最后一份共享指针被销毁，共享的对象才会被释放。因为`boost::scoped_ptr`能共享所有权，该智能指针可以拷贝，`boost::scoped_ptr`是不能的。

`boost::shared_ptr`定义在头文件`boost/shared_ptr.hpp`中。

例1.3 使用`boost:shared_ptr`
```
#include <boost/shared_ptr.hpp>
#include <iostream>

int main()
{
	boost:shared_ptr<int> p1{new int{1}};
	std::cout << *p1 << '\n';
	boost::shared_ptr<int> p2{p1};
	p1.reset(new int{2});
	std::cout << *p1.get() << '\n';
	p1.reset();
	std::cout << std::boolalpha << static_cast<bool>(p2) << '\n';
}
```

例1.3使用两个`boost::shared_ptr`类型的智能指针，**p1**和**p2**。**p2**用**p1**来初始化，这意味着这两个智能指针共享同一个`int`对象的所有权。在**p1**上调用`reset()`时，一个新的`int`对象纳入**p1**中。这不表示现存的`int`对象被销毁了。它还存于**p2**中，所以会继续存在。调用`reset()`之后，**p1**是持有数字2的`int`对象的唯一所有者，**p2**是持有数字1的对象的唯一所有者。

`boost::shared_ptr`内部使用引用计数。只有当`boost::shared_ptr`检测到该智能指针的最后一份拷贝被销毁了，被包含的对象才会使用`delete`释放。

和`boost::scoped_ptr`一样，`boost::shared_ptr`重载了`operator bool()`，`operator*()`和`operator->()`。提供了`get()`和`reset()`成员函数来获取当前存储的地址或者存储一个新的地址。

一个删除器（deleter）可以作为第二个参数传递给`boost::shared_ptr`。这个删除器必须是一个函数或函数对象，它接受一个`boost::shared_ptr`实例化所有的类型的指针。这个删除器会代替`delete`在析构函数中调用。这使得可以在`boost::shared_ptr`中管理非动态分配对象的其他资源。

例1.4 带有用户自定义删除器的`boost::shared_ptr`
```
#include <boost/shared_ptr.hpp>
#include <Windows.h>

int main()
{
	boost:shared_ptr<void> handle(OpenProcess(PROCESS_SET_INFORMATION, FALSE,
		GetCurrentProcessId(), CloseHandle));
}
```

在例1.4中`boost::shared_ptr`用void实例化。传入构造函数的第一个参数是`OpenProcess()`的返回值。`OpenProcess()`是一个Windows函数，获取一个进程的句柄。这个例子中，`OpenProcess()`返回当前进程的一个句柄——这个例子程序本身。

Windows使用句柄指向资源。一旦资源不再使用了，句柄必须使用`CloseHandle()`关闭。`CloseHandle()`期望的唯一参数是要关闭的句柄。在本例中，`CloseHandle()`作为第二个参数传入`boost::shared_ptr`的构造函数。`CloseHandle()`是**handle**的删除器。当**handle**在`main()`结束时销毁时，析构函数调用`CloseHandle()`来关闭作为第一个参数传递给构造函数的句柄。

#### 注意

例1.4 可以工作因为Windows句柄定义为`void*`类型。如果`OpenProcess()`不返回`void*`类型或者`CloseHandle()`不期望一个`void*`类型的参数，就不能在该例中使用`boost::shared_ptr`。删除器并不能使`boost::shared_ptr`成为可以管理任意资源的银弹。

例1.5 使用`boost::make_shared`
```
#include <boost/make_shared.hpp>
#include <typeinfo>
#include <iostream>

int main()
{
	auto p1 = boost::make_shared<int>(1);
	std::cout << typeid(p1).name() << '\n';
	auto p2 = boost::make_shared<int[]>(10);
	std::cout << typeid(p2).name() << '\n';
}
```

Boost.SmartPointers在`boost/make_shared.hpp`中提供了一个帮助函数`boost::make_shared()`。使用`boost::make_shared()`你可以创建一个`boost::shared_ptr`类型的智能指针，而不用自己调用`boost::shared_ptr`的构造函数。

`boost::make_shared()`的优势是动态分配的对象的内存和智能指针使用的引用计数的内存，在内部可以保留在同一块内存上。使用`boost::make_shared()`比调用`new`创建一个动态分配的对象，然后在`boost::shared_ptr`的构造函数中再次调用`new`来为引用计数分配内存更高效。

你也可以为数组使用`boost::make_shared`。在例1.5中，`boost::make_shared()`第二次调用后，**p2**中存放了一个有10个元素的数组。

`boost::shared_ptr`自Boost 1.53.0后支持数组。`boost::shared_array`提供了一个与`boost::shared_ptr`的智能指针，正如`boost::scoped_array`与`boost::scoped_ptr`类似一样。使用Visual C++ 2013和Boost 1.53.0或更新版本编译时，示例1.5为**p2**打印`class boost::shared_ptr<int [0]>`。

自Boost 1.53.0后，`boost::shared_ptr`支持单个对象和数组，自动检测使用`delete`还是`delete []`来释放资源。因为`boost::shared_ptr`也重载了`operator[]`（自Boost 1.53.0），该智能指针可以替代`boost::shared_array`。

例1.6 使用`boost::shared_array`
```
#include <boost/shared_array.hpp>
#include <iostream>

int main()
{
	boost::shared_array<int> p1{new int[1]};
	{
		boost::shared_array<int> p2{p1};
		p2[0] = 1;
	}
	std::cout << p1[0] << '\n';
}
```

`boost::shared_array`是对`boost::shared_ptr`的补充。因为`boost::shared_array`在析构函数中调用`delete[]`，它能被用于数组。对低于Boost 1.53.0的旧版本中，只能用`boost::shared_array`来处理数组，因为`boost::shared_ptr`不支持数组。

`boost::shared_array`定义在`boost/shared_array.hpp`中。

在例1.6中，智能指针**p1**和**p2**共享动态分配的`int`数组的所有权。当在**p2**中通过`operator[]`来存储数字1，可以通过**p1**来访问同一数组。因此，示例向标准输出中写入`1`。

像`boost::shared_ptr`一样，`boost::shared_array`使用一个引用计数。当**p2**销毁时，这个动态分配的数组不是被释放，因为**p1**依然持有对这个数组的引用。只有在`main()`的末尾，**p1**的作用域结束时，该数组才会销毁。

`boost::shared_array`也支持成员函数`get()`和`reset()`。而且，它重载了`operator bool`操作符。

例1.7 使用`BOOST_SP_USE_QUICK_ALLOCATOR`的`boost::shared_ptr`
```
#define BOOST_SP_USE_QUICK_ALLOCATOR
#include <boost/shared_ptr.hpp>
#include <iostream>
#include <ctime>

int main()
{
	boost::shared_ptr<int> p;
	std::time_t then = std::time(nullptr);
	for (int i = 0; i < 1000000; i++)
		p.reset(new int{i});
	std::time_t now = std::time(nullptr);
	std::cout << now - then << '\n';
}
```

优化使用像`boost::shared_ptr`的智能指针，而不是标准库中的，是有意义的。因为Boost.SmartPointers提供了可以优化智能指针行为的宏。例1.7使用宏`BOOST_SP_USE_QUICK_ALLOCATOR`激活Boost.SmartPointers自带的一个分配器。这个分配器管理内存块以减少为引用计数调用`new`和`delete`的次数。示例调用`std::time()`测量循环前后的时间。执行循环所需时间与计算机有关，示例使用`BOOST_SP_USE_QUICK_ALLOCATOR`可能运行得更快。Boost.SmartPointers的文档没有提到`BOOST_SP_USE_QUICK_ALLOCATOR`。因此，你应当对程序做性能分析，比较使用和未使用`BOOST_SP_USE_QUICK_ALLOCATOR`的结果。

### 提示
除了`BOOST_SP_USE_QUICK_ALLOCATOR`，Boost.SmartPointers还支持像`BOOST_SP_ENABLE_DEBUG_HOOKS`的宏。宏的名字以BOOST_SP_开头，方便在头文件中搜索，获取可用宏的概览。

[1]: http://www.boost.org/doc/libs/1_66_0/libs/smart_ptr/doc/html/smart_ptr.html