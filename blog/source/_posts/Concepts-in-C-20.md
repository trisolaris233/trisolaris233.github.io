---
title: C++20之Concepts
tags:
  - C++
categories:
  - Coding
abbrlink: 44510
date: 2020-03-18 13:44:55
---


## 介绍

`concepts`是`C++20`的核心功能, 是泛型编程中的一个重要的概念。可以看一看[cppreference](https://en.cppreference.com/w/cpp/language/constraints)中的介绍:

{% blockquote %}
Class templates, function templates, and non-template functions (typically members of class templates) may be associated with a constraint, which specifies the requirements on template arguments, which can be used to select the most appropriate function overloads and template specializations.

Named sets of such requirements are called concepts.
{% endblockquote %}

简单说来，`concepts`可以对模板参数进行约束，达到编译期进行检查的操作。为了方便更好的理解`concepts`的应用场景，可以举个简单的例子。

### 举一个简单的例子
考虑下面一个简单的代码，我们定义了模板函数f排序[start, end)两个迭代器的区间。
```C++
#include <iostream>
#include <algorithm>
#include <vector>

template <typename Iter>
void mysort(Iter&& start, Iter&& end) {
	std::sort(start, end);
}


int main() {
	std::vector<int> v{5,3,2,1,4};
	mysort(v.begin(), v.end());

	for (auto& item : v) std::cout << item << " ";

	return 0;
}
```

代码很成功的运行并输出了正确结果，然后另一个人看到以后就高高兴兴的写下了如下代码:

```C++
std::list<double> v2{5.5,3.2,3.1,4.4,1.5};
mysort(v2.begin(), v2.end());
```

然后高高兴兴的拿去编译了，没想到编译雷击，屏幕上输出了几十行错误消息，不忍直视
{% asset_img crazy_errors.png %}

实际上我们知道，我们调用的sort要求的是`random_access_iterator`,而list只有`bidirect_iterator`，但是编译器没有规定`forward_iterator`不能调用mysort函数，直到编译到sort的具体实现内容才发现问题，于是打印了许多看似不知所云的错误消息。

这就是一个典型的concepts的应用场景，也就是当我们需要对传入的模板参数类型做出种种规定的时候。比如在这里，我们认为，只有能提供`random access`的功能的`iterator`才能够调用mysort，否则就视为类型错误。

这就是Concepts可以解决的问题之一。

## Concepts解决了这样的问题

实际上，早在concepts出现以前，我们就可以使用`type_traits`的技巧实现这样的效果。可以考虑如下的实现方式：
```C++
template <typename T>
struct is_random_access {
	const static bool value = false;
};

template <typename U>
struct is_random_access<std::vector<U>> {
	const static bool value = true;
};

template <typename C, typename Iter>
void mysort(Iter&& start, Iter&& end) {
	if constexpr(is_random_access<C>::value) {
		std::sort(start, end);
	} else {
		std::cout << "failed: random_access_iterator is required" << std::endl;
	}
}
```
这段代码使用了类似于type_traits的方式，特化所有vector是支持random_access_iterator的，而不是vector的都是不可以random_access_iterator的，
这样的代码虽然可以运行，但是存在几个问题：

- 如果有一个新的支持random access的容器出现，就要为这种容器特化一个is_random_access类，工作量会增加
- 这种方式代码是可以正常编译的，但是错误是运行时的。虽然运行结果是确定的，但是依然要运行程序才可以看到显示
- 之前写的main函数也需要改，因为这里添加了一个新的模板参数C用来代表容器，所以调用mysort排序原来的v要改成`mysort<decltype(v)>(v.begin(),v.end())`，排序v2也一样。

可以看到用type_traits的方法解决不够优雅，也没有完全解决我们的需求。这种情况下就可以使用concepts.

### 使用concepts来取代type_traits

```C++
template <typename T>
concept is_random_access = requires(T x, int i) {
	x[i]; *(x + i); *(x - i);
};

template <typename Iter>
requires is_random_access<Iter>
void mysort(Iter&& start, Iter&& end) {

	std::sort(start, end);
}

int main() {
	std::vector<int> v{5,3,2,1,4};
	mysort(v.begin(), v.end());

	for (auto& item : v) std::cout << item << " ";
	std::cout << std::endl;

	std::list<double> v2{5.5,3.2,3.1,4.4,1.5};
	mysort(v2.begin(), v2.end());

	return 0;
}
```

初次接触`concepts`的人可能看不懂这个代码代表着什么。 第一行定义了一个`Concepts`，前面说过，`concepts`就是一系列`requirement`的集合，所以接下来我们写的是`requires`, 我们的requirements是要求T为`random_access_iterator`，那么怎么样才算是一个`random_access_iterator`呢？支持随机访问的就算。

所以`requires`相当于定义了一个匿名函数，对x进行花括号内的操作，如果这些操作对x是成立的。那么这个requirement就是被满足的。下面对mysort的定义，在template下面一行的requires表明所有的传入的Iter必须支持使is_random_access满足，否则直接报错。可以看看用了concepts后的报错,是不是简洁了不少，一目了然？

{% asset_img concepts_error.png %}
