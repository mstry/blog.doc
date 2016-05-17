---
title: CentOS7安装使用codeviz
date: 2016-05-15 16:33:59
tags:
---

# 简介<a id="orgheadline1"></a>

codeviz是一个分析c/c++项目函数调用关系的工具，基本思路是给gcc打上补丁，
用这个打了补丁的gcc编译要分析的c/c++项目，编译过程中会把函数调用关系dump成文本文件(.cdepn)。
再用perl脚本(genfull和gengraph)把调用关系文件转成graphviz的dot文件，并用graphviz转成图片。

```bash
yum -y install perl graphviz
```

# 安装<a id="orgheadline6"></a>

## 获取源码<a id="orgheadline2"></a>

- codeviz最新版1.0.12

    ```bash
    wget http://www.csn.ul.ie/~mel/projects/codeviz/codeviz-1.0.12.tar.gz
    ```
    
- codeviz-1.0.12匹配的gcc是3.4.6和4.6.2，选择4.6.2

    ```bash
    # 使用中科大的GNU镜像服务器
    wget http://mirrors.ustc.edu.cn/gnu/gcc/gcc-4.6.2/gcc-4.6.2.tar.gz
    ```

## 编译安装<a id="orgheadline5"></a>

解压codeviz: `tar xzvf codeviz-1.0.12.tar.gz`, codeviz官方主页的安装说明

```bash
cd codeviz-1.0.12
./configure && make && make install
```
    
运气好的话，可以一次成功；不过凡人运气有限，尽量不用在小事上。下面说明如何一步一步安装。

### 编译gcc-4.6.2<a id="orgheadline3"></a>

```bash
# 编译gcc的依赖
yum install gmp-devel mpfr-devel libmpc-devel glibc-devel glibc-devel.i686
# 如果codeviz-1.0.12/compilers下没有gcc-4.6.2，安装脚本会从gnu/gcc官网下载，很慢！
# 所以要事先从国内服务器下载
cp gcc-4.6.2.tar.gz codeviz-1.0.12/compilers
cd codeviz-1.0.12/compilers
# 先编译，排除错误编译通过后，再make install
./install_gcc-4.6.2.sh /usr/local/gcc-graph compile-only
cd gcc-graph/objdir
sudo make install
```

install\_gcc-4.6.2.sh默认使能共享库 `--enable-shared` 并自举编译 `make bootstrap` 

- 共享库要指明编译参数 `-fPIC`
- 自举编译多半不能编译通过，所以要去使能自举编译 `--disable-bootstrap`
- 为了加快编译，可以指明用4个线程编译 `make -j 4`

根据这三点，给install\_gcc-4.6.2.sh打补丁，如下：

```diff
diff -pruN install_gcc-4.6.2-old.sh install_gcc-4.6.2.sh
--- install_gcc-4.6.2-old.sh
+++ install_gcc-4.6.2.sh
@@ -32,8 +32,8 @@
cd ../objdir

# Configure and compile
-../gcc-4.6.2/configure --prefix=$INSTALL_PATH --enable-shared --enable-languages=c,c++ || exit
-make bootstrap
+CFLAGS="-fPIC" ../gcc-4.6.2/configure --prefix=$INSTALL_PATH --disable-bootstrap --enable-shared --enable-languages=c,c++ || exit
+make -j 4

RETVAL=$?
PLATFORM=i686-pc-linux-gnu
```

### 安装codeviz-1.0.12<a id="orgheadline4"></a>

codeviz绘制调用图用到两个perl脚本genfull和gengraph，其中，genfull依赖于perl-DB\_File

```bash
yum -y install perl-DB_File
cd codeviz-1.0.12
./configure
sudo make install-codeviz
```

# 使用<a id="orgheadline9"></a>

## codeviz gcc-4.6.2编译nginx<a id="orgheadline7"></a>

- 安装依赖

    ```bash
    yum -y install pcre openssl-devel
    ```
    
- 配置编译

    ```bash
    CC=/usr/local/gcc-graph/bin/gcc auto/configure
    make
    ```

## 生成调用图<a id="orgheadline8"></a>

- 查看参数说明

    ```bash
    # genfull参数说明
    genfull -h
    # gengraph参数说明
    gengraph -h
    ```
        
- 生成调用关系和被调关系

    ```bash
    cd nginx
    # 生成总图dot文件，nginx目录下生成文件full.graph
    genfull
    # ngx_daemon为起点，深度为1的调用关系图
    gengraph -f ngx_daemon -d 1 --output-type=png
    # ngx_daemon为终点，深度为1的被调关系图
    gengraph -f ngx_daemon -d 1 -r --output-type=png
    ```
