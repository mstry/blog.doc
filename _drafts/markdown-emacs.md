---
title: 用emacs写markdown
date: 2017-03-09 21:28:06
categories:
tags:
---

# 1. 安装markdown-mode #

## 1.1 在线安装 ##

- 导入package.el

	```lisp
	;; add to .emacs or init.el
	(require 'package)
	(add-to-list 'package-archives
		'("melpa-stable" . "https://stable.melpa.org/packages/"))
	(package-initialize)
	```

- 执行安装
	emacs minibuf执行 `M-x package-install RET markdown-mode RET`
	
## 1.2 直接下载 ##

```bash
# 下载到任意一个load-path
cd /usr/share/emacs/site-lisp/
wget http://jblevins.org/projects/markdown-mode/markdown-mode.el
```

```lisp
;; 配置.emacs
(autoload 'markdown-mode "markdown-mode"
   "Major mode for editing Markdown files" t)
(add-to-list 'auto-mode-alist '("\\.markdown\\'" . markdown-mode))
(add-to-list 'auto-mode-alist '("\\.md\\'" . markdown-mode))

(autoload 'gfm-mode "markdown-mode"
   "Major mode for editing GitHub Flavored Markdown files" t)
(add-to-list 'auto-mode-alist '("README\\.md\\'" . gfm-mode))
```

# 2. 使用pandoc生成html #

- 安装pandoc和python-pygments
	
	```bash
	yum -y install pandoc python-pygments
	```
	
- 配置.emacs

	```lisp
	(custom-set-variables
	'(markdown-command "pandoc -c markdown.css -f markdown -t html -s --mathml --highlight-style pygments --self-contained"))
	```
	
# 3. markdown-mode常用快捷键 #

# 4. 参考资料 #
- [Emacs Markdown Mode](http://jblevins.org/projects/markdown-mode/)
