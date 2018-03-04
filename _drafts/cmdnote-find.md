---
title: find命令
date: 2016-06-30 07:34:19
categories:
tags:
---

> 没有什么能比得上探索和发现新的人、地方、事物所带来的刺激。领域可能有所不同，但有些原则却是一样的。在这些原则中，有一条是记录下您的旅程，另一条则是了解和使用工具。
> UNIX®操作系统很像一片广阔的、未经标识的荒野。当您在这样的领域中旅行时，可以选择一些日后能够给您带来帮助的工具。find命令便是这样一种工具。find命令不仅能够简单地用来定位文件，它还可以自动地执行其他UNIX命令的序列，其中使用所查找到的文件名作为输入。

find和locate是linux环境下用来查找文件的常用命令，但它们的工作方式完全不同，下面记录一下find命令的基本用法。

# 1. 命令格式 #

```bash
find <path> <expression>
```

# 2. 查找文件 #

## 2.1 匹配文件名和路径 ##

- name
  name选项直接匹配文件名，可以使用3个特殊字符"* ? []"，但这可不是正则表达式。
  ```bash
  # 当前路径开始，查找hello.c
  find . -name 'hello.c'
  
  # 绝对路径/etc开始，查找sshd_config
  find /etc -name 'sshd_config'
  
  # 当前路径开始，查找所有.c文件
  find . -name '*.c'
  
  # 当前路径开始，查找所有.h和.c文件
  find . -name '*.[hc]'
  ```

- regex和iregex
  与name选项匹配文件名不同，这两个选项是用base-regexp匹配文件路径，iregex忽略大小写。
  ```bash
  # 当前路径开始，查找所有.c文件
  find . -regex '.*\.c'
  
  # 当前路径开始，查找所有.h和.c文件
  find . -regex '.*\.h\|.*\.c'
  ```

## 2.2 匹配文件属性 ##

```bash
# 当前路径开始查找，最近10分钟内访问的文件(access time)
find . -amin -10

# 当前路径开始查找，最近2天(或48小时)访问的文件
find . -atime -2

# 当前路径开始查找，最近5分钟修改过的文件(modify time)
find . -mmin -5

# 当前路径开始查找，最近24小时修改过的文件
find . -mtime -1

# 当前路径开始查找，空文件或者空文件夹
find . -empty

# 当前路径开始查找，用户组为tom的文件
find . -group tom

# 当前路径开始查找，属于用户为jerry的文件
find . -user jerry

# 当前路径开始查找，大于10000000字节的文件(c:字节，w:双字，k:KB，M:MB，G:GB)
find . -size +10000c

# 当前路径开始查找，出小于1000KB的文件
find . -size -1000k
```

## 2.3 匹配条件逻辑组合 ##

逻辑操作符与、或、非：`-and(-a) -or(-o) !`

```bash
#在/tmp目录下查找大于10000字节并在最后2分钟内修改的文件
find /tmp -size +10000c -and -mtime +2

#当前路径开始查找，用户是fred或者george的文件
find . -user fred -or -user george

#在/tmp目录下查找所有不属于panda用户的文件
find /tmp ! -user panda
```

# 3. 匹配文件并执行操作 #

- exec
  ```bash
  find . -name '*.[hc]' -exec ls -l {} \;
  find . -name '*.[hc]' -exec ls -l {} +
  ```
  参数中的"{}"是占位符，执行时替换为找到的文件；exec选项执行的命令必须以" ;"空格分号结尾，而且';'还要反斜线转义。" ;"与" +"区别在于，前者是一条一条执行，后者与xargs是把所有找到的文件拼成一个字符串来执行。细心的同学已经发现，拼接字符串的长度可能会超限，而且如果文件名中有空格引号反斜线等字符也会出问题。所以，建议用" ;"方式，并尝试用引号将占位符"{}"包起来。例如
  ```bash
  find . -name '*.[hc]' -exec ls -l "{}" \;
  ```
  
- xargs
  ```bash
  find . -name '*.[hc]' | xargs -0 ls -l
  ```
  xargs命令"-0"选项可以处理空格引号反斜线等字符问题，还是难免字符串超常的问题。

# 参考资料 #

- [http://www.ibm.com/developerworks/cn/aix/library/es-unix-find.html](使用UNIX find 命令的高级技术)
- [http://www.cnblogs.com/zhangmo/p/3571735.html](linux下的find文件查找命令与grep文件内容查找命令)
- Linux manual: FIND(1) XARGS(1)
