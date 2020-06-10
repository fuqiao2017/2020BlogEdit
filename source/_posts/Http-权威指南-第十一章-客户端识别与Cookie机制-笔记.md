---
title: Http 权威指南 第十一章 客户端识别与Cookie机制 笔记
date: 2020-06-10 10:02:23
tags:
---
##### 不可靠的识别，一般爬虫可能使用: 
Http 首部: From、User-Agent、Referer

##### 客户端IP地址:
1. IP 地址会变化
2. 通过网络地址转换(Network Address Transition NAT)可以隐藏真实的 IP 地址
3. 经过代理或网关转发的新连接，导致服务器只看到代理的IP地址 (有些代理会添加扩展的         Client-IP\X-Forwarded-For 首部)

##### 用户登录
Http 内建机制: 通过 WWW-Authenticate 首部和 Authorization 首部向 Web 站点传送用户相关信息(服务器回送 401 Login Required 状态码)
缺点: 繁琐，体验极差

##### 胖URL
