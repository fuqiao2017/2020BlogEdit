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

#### cookie
实现持久会话的最好方式
- 分类:
***会话cookie*** : 用户退出浏览器即删除 
***持久cookie*** : 存储在硬盘上
两者唯一的区别就是过期时间，如果设置了 Discard 参数，或者没有设置 Expires 或 Max-Age 参数来说明扩展的过期时间，这个 cookie 就是一个会话 cookie

#### cookie 基本思想:
让浏览器积累一组服务器特有的信息，每次访问服务器时都将这些信息提供给它。
因为浏览器要负责存储 cookie 信息，所以此系统被称为 ***客户端侧状态(client-side state)***
#### cookie 规范正式名称: HTTP 状态管理机制(HTTP state management mechanism)

#### cookie 版本
- 版本0:
  Set-Cookie 首部: 
  Cookie 首部: 将所有与 域、路径和安全过滤器匹配的未过期 cookie 都发送，且所有 cookie 都被组合到一个 Cookie 首部中
- 版本1:
  Set-Cookie2 首部: 多了一些可用属性，比如 Discard(说明为会话cookie)、Max-Age(相对秒数)、Port
  Cookie2 首部: 携带更多的信息

#### cookie 与会话跟踪

#### cookie 与缓存
私有的内容不可缓存

#### cookie 安全问题

构建用户跟踪: 将持久 cookie 与 IP 地址、Referer 结合可以构建相当精确的用户档案和浏览模式信息


