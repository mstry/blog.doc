---
title: Kerberos认证协议
date: 2017-05-07 11:54:49
categories:
tags:
---

Kerberos认证所使用的信息根本上是“帐号/密码”，下面就看一下Kerberos是如何使用和传输这个认证信息的。

# 1. 基本概念 #

介绍Kerberos认证协议，先介绍其认证过程中涉及的基本概念。

## 1.1 认证主体 ##

Kerberos认证集群中存在三种主体角色

- 客户端 Client
- 服务端 Server
- 密钥分发中心 KDC

需要认证的进程可能是Client，也可能是Server，或者同时是二者。KDC是Client和Server共同信任的第三方，管理着所有Client和Server的帐号信息和和主密钥。
KDC是Kerberos的核心服务，包含两种服务AS(Authentication Service)和TGS(Ticket Granting Service)。

## 1.2 认证信息 ##

- Long-term Key：长期密钥，比如以帐号密码或者基于帐号密码的转换作为密钥，下文称作Master Key。
- Short-term Key：短期密钥，比如在一个会话或一定期限内有效的密钥，下文称作Session Key。
- 帐号信息：可能是用户名，或者附带其他如地址电话公司组织等信息，下文称作Principal。

认证所使用的“帐号/密码”信息，就对应着"Master Key/Principal"。Kerberos认证使用的Master Key是对帐号密码哈希转换得到的。网络传输的数据不建议用长期密钥加密，因为没有绝对安全的密钥，如果有恶意攻击者截获加密数据，破解密钥只是时间长短的问题。如果使用短期密钥加密网络中传输的数据，即时密钥被解密，可能早已超出密钥的有效期。

Kerberos使用对称加密，对认证信息进行加密，并以两种形式在网络中传输：认证子(Authenticator)和票据(Ticket)。

- Authenticator：认证子，包含时间戳和Principal，使用Master Key或Session Key加密。
	- Client/AS Authenticator：Client Master Key加密的时间戳
	- Client/Server Authenticator：Client Principal和时间戳打包后，用Session Key加密
- Ticket：票据，包含Session Key和Principal，使用Master Key加密。
	- TGT (Ticket Granting Ticket): 授票票据，用于访问TGS，由AS授予，用KDC Master Key加密。
	- SST (Server Session Ticket)：服务票据，用于访问Server服务，由TGS授予，用Server Master Key加密。

# 2. 认证过程 #

## 2.1 认证协议 ##

Kerberos认证协议包含三个子协议：

### Client/AS ###

Client用自己的Master Key加密时间戳作为Authenticator发给AS进行认证，AS从KDC管理的密钥库中查到Client的Master Key，用其解密Authenticator，校验时间戳是否合法。如果时间戳合法，则认证通过，AS通过KDC分配Session Key，将该Session Key与Client Principal打包用KDC的Master Key加密作为TGT，与用Client Master Key加密的Session Key一起返回给Client。Client收到返回信息后，可以用自己的Master Key解密得到其与TGS之间的Session Key。
   
### Client/TGS ###

Client把时间戳和Principal打包，用其在AS处申请到的，与TGS之间的Session Key进行加密，作为Authenticator连同TGT一起发给TGS进行认证。TGS用KDC的Master Key解密TGT得到Principal和Session Key，再用该Session Key解密Authenticator得到时间戳和Principal，这两个信息与TGT中的Principal进行对比认证。如果认证通过，则TGS从KDC申请Session Key，将该Session Key与Client Principal打包用Client将要访问的Server的Master Key加密作为SST，与用Client Master Key加密的Session Key一起返回给Client。Client收到返回信息后，可以用自己的Master Key解密得到其与Server之间的Session Key。
   
### Client/Server ###

Client把时间戳和Principal打包，用其在TGS处申请到的，与Server之间的Session Key进行加密，作为Authenticator连同SST一起发给Server进行认证。Server用自己的Master Key解密SST得到Principal和Session Key，再用该Session Key解密Authenticator得到时间戳和Principal，这两个信息与SST中的Principal进行对比认证。如果认证通过，则Client可与Server建立会话，进行执行后续请求。

## 2.2 认证流程 ##

现在考虑某个Client要访问某个Server的情景，直接的思路是Client从KDC申请一个与Server通信的Session Key，然后KDC将这个Session Key分发给Client和Server。这样的话，如果Server有多个Client连接的话，就要维护一个Session Key的列表，造成服务端复杂话。更严重的情况是，因为网络传输延时的不确定，KDC分发密钥时，可能Client收到Session Key并向Server发起请求后，一直到请求超时，Server还没收到KDC分发的Session Key，造成认证失败。

Kerberos认证过程的处理思路是“谁发起认证，谁提供认证信息”，例如Client要访问Server，就要把认证信息和会话密钥同时发给Server。认证信息可以用会话密钥加密传输，而会话密钥的传输就用到前面介绍的两种票据TGT和SST。下面简述一下客户端访问服务端的认证流程。

1. Client访问Server：Client有访问访问Server的票据SST吗？如果有，直接用Session Key加密Authenticator与SST一起，发给Server进行认证。如果没有，需要向TGS申请SST。
2. Client访问TGS: Client有访问TGS的票据TGT吗？如果有，直接用Session Key加盟Authenticator与TGT一起，发给TGS进行认证。如果没有，需要向AS申请SST。
3. Client访问AS: Client用自己的Master Key加密时间戳作为Authenticator发给AS进行认证，如果认证通过，AS返回Client加密的Session Key和TGT。

