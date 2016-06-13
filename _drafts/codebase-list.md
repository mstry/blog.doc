---
title: 码库之链表
date: 2016-06-12 22:37:28
categories: Programming
tags:
	- linux
	- c
---

# 介绍 #

Linux kernel源码中有一个简单的双向链表很惊艳，其源文件为`include/linux/list.h`。
下面摘录关键代码进行分析。

## 构造链表 ##

```c
/*
 * Simple doubly linked list implementation.
 *
 * Some of the internal functions ("__xxx") are useful when
 * manipulating whole lists rather than single entries, as
 * sometimes we already know the next/prev entries and we can
 * generate better code by using them directly rather than
 * using the generic single-entry routines.
 */

struct list_head {
	struct list_head *next, *prev;
};

#define LIST_HEAD_INIT(name) { &(name), &(name) }

#define LIST_HEAD(name) \
	struct list_head name = LIST_HEAD_INIT(name)

#define INIT_LIST_HEAD(ptr) do { \
	(ptr)->next = (ptr); (ptr)->prev = (ptr); \
} while (0)

```

## 添加节点 ##

```c
static inline void __list_add(struct list_head *new,
			      struct list_head *prev,
			      struct list_head *next)
{
	next->prev = new;
	new->next = next;
	new->prev = prev;
	prev->next = new;
}

/**
 * new添加到head之前
 * 可用于实现栈
 */
static inline void list_add(struct list_head *new, struct list_head *head)
{
	__list_add(new, head, head->next);
}

/**
 * new添加到head之后
 * 可用于实现队列
 */
static inline void list_add_tail(struct list_head *new, struct list_head *head)
{
	__list_add(new, head->prev, head);
}
```

## 删除节点 ##

```c
/*
 * 将prev和next连接起来，删除中间节点
 */
static inline void __list_del(struct list_head * prev, struct list_head * next)
{
	next->prev = prev;
	prev->next = next;
}

static inline void list_del(struct list_head *entry)
{
	__list_del(entry->prev, entry->next);
	entry->next = LIST_POISON1;
	entry->prev = LIST_POISON2;
}
```

## 获取节点 ##

```c
/**
 * list_entry - get the struct for this entry
 * @ptr:	the &struct list_head pointer.
 * @type:	the type of the struct this is embedded in.
 * @member:	the name of the list_struct within the struct.
 */
#define list_entry(ptr, type, member) \
	container_of(ptr, type, member)
```

## 遍历 ##

## 其他 ##

```c
/*< 判断链表是否为空 */
static inline int list_empty(const struct list_head *head)
{
	return head->next == head;
}
```

### container_of ###

```c
/**
 * include/linux/stddef.h
 */
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)

/**
 * include/linux/kernel.h
 * container_of - cast a member of a structure out to the containing structure
 *
 * @ptr:	the pointer to the member.
 * @type:	the type of the container struct this is embedded in.
 * @member:	the name of the member within the struct.
 *
 */
#define container_of(ptr, type, member) ({			\
        const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
        (type *)( (char *)__mptr - offsetof(type,member) );})

```

此处隆重介绍以下`container_of`，这个宏可以根据结构体中某个成员的指针，得到该结构体的指针。
乍看来没什么用，什么情况下会得到成员的指针，而不知道整个结构体的指针呢？
实际上在构造通用数据结构的时候，是灰常有用滴，比如此处的list。
有一点需要说明，`typeof`不是C语言标准的关键字，就是说`container_of`只在gcc编译环境，
或者说支持`typeof`关键字的编译环境下可用。不过，即使当前使用的编译器编译器不支持`typeof`，
通常也会有替代方案，稍加改装以下，还是可以构造出`container_of`。

理解`container_of`实现思路，可以考虑两种情况：
- `member`是结构体的第一个成员
  这种情况最简单，结构体的指针为`(type *)ptr`
- `member`不是结构体的第一个成员
  这种情况的思路也很明确`(type *)(ptr - offset)`
  另外，你知道指针的加减运算是跟指针类型相关的，所以计算结构体指针应为`(type *)((char *)ptr - offset)`
  那么，`member`成员相对与结构体起始地址的偏移`offset`怎么获得呢？把结构体定位到地址0，
  `member`的地址实际上就是“相对偏移”，这就是`offsetof`宏的实现思路。`__mptr`则是为对`ptr`进行类型检查。
  

# 应用 #


