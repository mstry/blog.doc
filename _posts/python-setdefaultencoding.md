---
title: python默认编码设置
categories: programming
tags: python
date: 2016-05-18 22:16:54
---


# 介绍 #

python内部编码(字解码)unicode，假设现在要比较两个字符串是否相等，在python2中字符串类型等价于字节数组，两个字节数组相同并不意味着两个字符串相等，因为它们可能是由不同的字符按照不同的编码方式生成的恰好相同的字节数组。

所以，在处理字符串时，python会根据字符串的编码格式，先将字符解码成内部编码unicode。如果一个str对象没有显式调用decode指明编码系统，python就会用默认的系统编码格式，来解码字符串。

```python
import sys
sys.getdefaultencoding()  # check python default encoding, usually 'ascii'
```

# 设置方法 #

python2.5之后无法调用sys.setdefaultencoding()函数来修改默认编码，因为python在启动的时候会调用site.py文件，在这个文件中设置完默认编码后会删除sys的setdefaultencoding方法。不能再被调用了.  在确定sys已经导入的情况下, 可以reload sys这个模块之后, 再 sys.setdefaultencoding('utf8')

## 临时设置 ##

```python
import sys
reload(sys)
sys.setdefaultencoding('utf-8')
sys.getdefaultencoding()
```

## 永久设置 ##

- 编辑site.py，修改setencoding()方法，不推荐使用该方法
- 在site-packages或/etc/python2.7目录下，新建sitecustomize.py，加入设置代码

  ```python
  # filename: sitecustomize.py
  import sys
  sys.setdefaultencoding('utf-8')
  ```
sitecustomize.py 是在 site.py 被import 执行的, 因为 sys.setdefaultencoding() 是在 site.py 的最后删除的, 所以, 可以在 sitecustomize.py 使用 sys.setdefaultencoding(). 


# 注意事项 #

python2中避免字符编码错误的几个建议：

- 所有text string都应该是unicode类型，而不是str，即字符串的声明方式为: s = u'xxxx'。
- 在需要转换的时候，显式转换。从字节解码成文本，用var.decode(encoding)，从文本编码成字节，用var.encode(encoding)。
- 从外部读取数据时，默认它是字节，然后decode成需要的文本；同样的，当需要向外部发送文本时，encode成字节再发送。

# 参考资料 #

- [python default coding](http://www.cnblogs.com/samlee/archive/2012/03/19/2406510.html)
- [立即停止使用 setdefaultencoding('utf-8')， 以及为什么](http://blog.ernest.me/post/python-setdefaultencoding-unicode-bytes)

