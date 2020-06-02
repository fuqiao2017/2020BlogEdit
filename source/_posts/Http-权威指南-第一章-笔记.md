---
title: Http 权威指南 第一章 笔记
date: 2020-06-02 09:59:15
tags:
---
## Http 概述
``` bash
Web 资源：所有你能想到的格式
```
所有类型的内容来源都是资源  

``` bash
MIME: (Multipurpose Internet Mail Extension 多用途因特网邮件扩展)
```
MIME 类型是一种文本标记，表示一种主要的对象类型和一个特定的子类型，text/html  

``` bash
URI: (Uniform Resource Identifier 统一资源标识符)
```
两种形式：URL 和 URN  
URL: (Uniform Resource Locator 统一资源定位符)，url 说明了 协议、服务器和服务器资源  
URN: (Uniform Resource Name 统一资源名)，处于试验阶段，这种框架可以在对象从一处搬移到另一处时，保持
稳定的访问名称
现在，几乎所有的 URI 都是 URL  

### 基本的浏览器连接处理
1. 用户输入 url
2. 浏览器解析 主机名 通过 DNS 服务转换成 服务器IP 地址
3. 解析端口号，默认是 80
4. 建立一条与服务器对应端口的 TCP 连接
5. 向服务器发送一条 HTTP 请求报文
6. 服务器回送一条响应报文
7. 关闭连接，浏览器显示文档

``` bash
Http: (HyperText Transfer Protocol 超文本传输协议)
```
Http/1.1: 当前使用的版本，Http-NG (Http/2.0)
Http 报文: 起始行、首部、主体
