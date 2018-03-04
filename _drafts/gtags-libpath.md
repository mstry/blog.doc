---
title: gtags索引本地库文件
date: 2017-03-09 22:29:16
categories:
tags:
---

- 创建本地库索引
	```bash
	mkdir -p ~/.gtags
	cd ~/.gtags
	ln -s /usr/include usr-include
	ln -s /usr/local/include usr-local-include
	gtags -c
	```
- 导出GTAGSLIBPATH
	```bash
	# ~/.bashrc
	export GTAGSLIBPATH=$HOME/.gtags/
	```
