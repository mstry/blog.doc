---
title: CentOS7最小系统安装xfce4
date: 2016-06-07 20:53:21
categories: linux
tags:
    - centos7
    - xfce4
---

# 1. 制作启动U盘 #

## 1.1 下载系统镜像 ##

从镜像网站下载CentOS7最小系统镜像，可以用网易或中科大网站，此处选中科大。

```bash
wget http://mirrors.ustc.edu.cn/centos/7.2.1511/isos/x86_64/CentOS-7-x86_64-Minimal-1511.iso
# 顺便下载mirror repo文件
wget https://lug.ustc.edu.cn/wiki/_export/code/mirrors/help/centos?codeblock=3 -O CentOS-Base.repo
```

## 1.2 制作启动U盘 ##

```bash
# 假设U盘的设备名称为/dev/sdb
dd if=CentOS-7-x86_64-Minimal-1511.iso of=/dev/sdb bs=1M
```

# 2. 安装系统 #

设置BIOS从U盘启动，以及分区安装CentOS，设置网络和用户名等等，略去不表。

# 3. 安装xfce4 #

```bash
# s1. 更新repo，替换为国内的源
cp CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
yum clean all
yum makecache

# s2. 系统升级到最新
yum update

# s3. 安装epel
yum -y install epel-release

# s4. 安装xorg服务和驱动
yum -y install xorg-x11-server-Xorg xorg-x11-drivers

# s5. 安装xfce4
yum -y groupinstall Xfce

# s6. 设置图形启动
systemctl set-default graphical.target
```

# 4. 其它 #

## 4.1 使用lightdm ##

Xfce group默认使用gdm做为display manager，可以替换为轻量级的lightdm，
同时安装相应的锁屏工具light-locker。

```bash
yum -y install lightdm light-locker
systemctl disable gdm
systemctl enable lightdm
```

设置锁屏快捷键`Applications->Settings->Keyboard->Application Shortcuts->Add`，
`light-locker-command -l`: `Ctrl+Alt+L`

## 4.2 中文输入 ##

```bash
# s1. 安装文泉驿中文字体
yum -y install wqy-microhei-fonts wqy-zenhei-fonts
# s2. 安装ibus拼音输入法
yum -y install ibus ibus-libpinyin
```

`im-chooser`设置ibus为默认输入法；`ibus->Preferences`启用中文拼音输入法。

