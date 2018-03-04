---
title: 给终端点颜色
date: 2017-03-09 20:19:37
categories:
tags:
---

# 1. 预定义颜色 #

颜色预定义可以添加在 ~/etc/bashrc~

```bash
# /etc/bashrc
BLACK='\[\e[1;30m\]'
darkgray='\[\e[0;30m\]'
RED='\[\e[1;31m\]'
red='\[\e[0;31m\]'
GREEN='\[\e[1;32m\]'
green='\[\e[0;32m\]'
YELLOW='\[\e[1;33m\]'
yellow='\[\e[0;33m\]'
BLUE='\[\e[1;34m\]'
blue='\[\e[0;34m\]'
PURPLE='\[\e[1;35m\]'
purple='\[\e[0;35m\]'
CYAN='\[\e[1;36m\]'
cyan='\[\e[0;36m\]'
WHITE='\[\e[1;37m\]'
gray='\[\e[0;37m\]'
NC='\[\e[0m\]'              # No Color
```

# 2. 重定义用户提示符 #

```bash
# ~/.bashrc
PS1="[${red}\u${NC}@${cyan}\h ${yellow}\W ${NC}]\$ "
```
