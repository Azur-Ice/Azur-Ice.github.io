---
layout: post
title: AzurCloud - 开发笔记
date: 2020-08-28
tags: GoLang
---

## 端口号

范围：0~65535

- 常用端口号 <256

	- 保留TCP端口

		HTTP 80

		FTP 20/21

		Telnet 23

		SMTP 25

		DNS 53

	- 保留UDP端口

		DNS 53

		BootP 67(S)/68(C)

		TFTP 69

		SNMP 161
	
- 注册端口 1024~49151

- 动态/私有端口 49152~65535

（1）端口号小于256的定义为常用端口，服务器一般都是通过常用端口号来识别的。任何TCP/IP实现所提供的服务都用1---1023之间的端口号，是由ICANN来管理的；端口号从1024---49151是被注册的端口，也成为“用户端口”，被IANA指定为特殊服务使用 [6] ；

（2）[客户端](https://baike.baidu.com/item/客户端)只需保证该端口号在本机上是惟一的就可以了。[客户端](https://baike.baidu.com/item/客户端/101081)端口号因存在时间很短暂又称临时端口号 [6] ；

（3）大多数TCP/IP实现给临时端口号分配1024---5000之间的端口号。大于5000的端口号是为其他服务器预留的 [6] 。

## net/http 包
