---
title: Http 权威指南 第三章 HTTP报文 笔记
date: 2020-06-02 15:21:42
tags:
---

#### 报文流 
报文（请求报文、响应报文）是流动的，因此有上游、下游，报文流入源端服务器，并流回到客户端

#### 起始行
请求报文: 方法 url 版本
响应报文: 版本 状态码 原因短语
- ##### 方法
GET/HEAD/POST/PUT/DELETE/TRACE/OPTIONS
![](/images/http常用方法.jpeg)
- ##### 状态码
分类:
  1. 100 ~ 101 信息提示
  2. 200 ~ 299 成功
  3. 300 ~ 399 表示资源已经被移走，重定向
  4. 400 ~ 499 表示客户端请求出错了
  5. 500 ~ 599 表示服务器端出错了  

#### 首部
首部包括: 通用首部、请求首部(如 Accept)、响应首部(如 Server: Golfe2)、实体首部(如 Content-Type)、扩展首部

***逐跳首部*** : 只适用于单条传输链路，是不会沿着链路传输下去的，比如 Connection