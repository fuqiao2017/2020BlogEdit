---
title: Http 权威指南 第十二章 基本认证机制 笔记
date: 2020-06-11 15:22:53
tags:
---
***关键词*** : 基本认证    质询/响应

- Http 认证形式:
***基本认证*** : basic authentication
***摘要认证*** : digest authentication
***质询/响应*** : challenge/response
请求 -> 质询 -> (用户名:密码)再次请求 -> 响应
状态码: 401
WWW-Authenticate: Basic realm 指定一个安全域
Authorization: Basic base-64-username:password

***Base-64 编码*** : 将一个8位字节序列划分为一些6位的块，用每个6位的块在一个特殊的由64个字符组成的字母表中选择一个字符

***代理认证*** : proxy authentication 通过代理服务器提供对某组织内部资源的统一访问控制
Proxy-Authenticate、Proxy-Authorization、Proxy-Authentication-Info

##### 基本认证的安全缺陷
1. 用户名密码属于“明文”传送
2. 加密后的信息可以被捕获和利用
将基本认证与加密数据传输(比如 SSL)配合使用，会使基本认证更安全
