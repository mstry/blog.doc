---
title: CentOS7 Samba简单设置
date: 2017-04-17 22:17:13
categories:
tags:
---

# 1. 安装配置 #

- 安装samba

    ```bash
	yum -y install samba
	```
	
- 配置

    ```ini
	# /etc/samba/smb.conf
	[homes]
		comment = Home Directories
		browseable = no
		writable = yes
		valid users = %S
		create mask = 0644
		directory mask = 0755
	```

- 添加用户

    ```bash
	# 添加系统用户到samba，并设置密码
	smbpasswd -a ${USER_NAME}
	```

- 重启服务

    ```bash
	systemctl start smb
	systemctl enable smb
	```


# 2. 系统设置 #

- 设置selinux
  
    ```bash
	# vi /etc/selinux/config
	SELINUX=permissive
	# 即时生效
	setenforce 0
	```
- 设置防火墙

    ```bash
	firewall-cmd --permanent --zone=public --add-serive=samba
	firewall-cmd --reload
	```
