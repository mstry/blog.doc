---
title: container_of中__mptr的作用
categories:
tags:
date: 2016-06-13 14:07:06
---


# 1. 引言 #

```c
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)

#define container_of(ptr, type, member) ({			\
        const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
        (type *)( (char *)__mptr - offsetof(type,member) );})
```

认识`container_of`这个宏以来，就对其中的`__mptr`不明觉厉，终于看到一篇令人信服的网文。

网上对`__mptr`的解释，主要分两种：
- 避免带运算符的宏参数展开到多处，造成多次运算
- 进行参数检查

# 2. 分析 #

## 2.1 宏展开的隐含风险 ##

假设宏定义如下：
```c
#define min(x,y) ((x) < (y) ? (x) : (y))
```
`min(a++, b++)`得到的可能就不是期望的值，因为宏参数展开到两处，进行了两次自增运算。

如果宏定义如下，就不会出现这个问题。

```c
#define min_t(type,x,y) \
({ type __x = (x); type __y = (y); __x < __y ? __x: __y; })
```

这种宏展开的风险，确实应该规避，但是似乎只有宏参数展开到多个地方的时候，才可能出问题；
而`container_of`的`ptr`只在一处展开，所以这种是比较牵强的。

## 2.2 参数检查 ##

即检查`ptr`是否确实是`type`结构体中`member`成员类型的指针，如果类型不匹配，编译器给出warning。
尽管编译警告经常被无视，但是严谨的项目通常会有`-Werror`编译选项，要求程序员正视所有警告。
毕竟`container_of`是kernel要用的，严谨！哈哈
					  
# 3. 参考资料 #
[关于container_of 第一行的__mptr作用的总结](http://bbs.chinaunix.net/thread-3618696-1-1.html)

