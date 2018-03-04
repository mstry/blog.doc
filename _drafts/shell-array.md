---
title: Shell数组操作
date: 2017-04-27 21:49:48
categories: 
tags: 
---

```bash
# 声明：圆括号，元素用空格分割，起始索引为0
a=("start" "stop" "restart" "status")
# 读取：${}
echo ${a[1]} # stop
echo ${a[*]} # start stop restart status
# 赋值
a[1]=hello && echo ${a[*]} # start hello restart status
# 遍历
for i in ${a[*]}; do echo $i; done
for i in ${a[@]}; do echo $i; done
# 判断是否包含元素
if [[ ${a[@]} =~ 'start' ]]; then echo YES; else echo NO; fi # YES
if [[ ${a[@]} =~ 'starter' ]]; then echo YES; else echo NO; fi # NO
b="remove"
if [[ ! ${a[@]} =~ $b ]]; then echo YES; else echo NO; fi # YES
# 数组长度
echo ${a[*]}
echo ${a[@]}
# 删除
unset a[1] && echo ${a[*]} # start restart status
unset a
```
