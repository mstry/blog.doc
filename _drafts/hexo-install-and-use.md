---
title: hexo安装使用
date: 2016-06-04 14:32:07
categories:
tags:
---

# 安装配置 #

```bash
sudo yum -y install nodejs npm
sudo npm install hexo-cli -g
hexo init blog
cd blog
npm install
```

# 基本操作  #


# NexT主题 #

## 安装启用 ##

## 调整代码字体 ##

代码字体默认大小为13px，看起来略小，改为14px。
当前使用的NexT版本号为5.0.1，代码字体定义在`source/css/_variables/base.styl`145行。

```css
// Code & Code Blocks
// --------------------------------------------------
$code-font-family               = $font-family-monospace
$code-font-size                 = 14px
$code-background                = $gainsboro
$code-foreground                = $black-light
$code-border-radius             = 4px
```

## 开启多说分享 ##


## 开启百度统计和推广 ##


# 参考资料 #
[Hexo官网文档](https://hexo.io/zh-cn/docs/)

