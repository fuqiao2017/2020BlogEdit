---
title: Http 权威指南 第九章 Web机器人 笔记
date: 2020-06-04 17:27:40
tags:
---
***关键词*** : 爬虫  根集  环路  URL别名  机器人规范  搜索引擎

***根集(root set)*** : 爬虫开始访问的 URL 初始集合 
***环路(cycle)*** : 爬虫陷入一种循环中
类型: 链接指向形成的环路、文件系统连接环路、动态虚拟Web空间
***避免循环和重复的技术*** :
1. 规范化URL
2. 广度优先的爬行
3. 节流: 限制重复页面总数和对同一服务器访问的总数
4. 限制 URL 的大小: 拒绝爬行超过一定长度的URL
5. URL/站点黑名单: 有导致爬虫环路和陷阱的站点
6. 模式检测
7. 内容指纹
8. 人工监视: 诊断和日志功能

#### 爬虫管理 URL 的技术
1. 数和散列表: 采用加速 URL 查找的数据结构
2. 有损的存在位图: 有损数据结构，对存在位置位
3. 检查点: 将已访问的 URL 保存到备份到硬盘上
4. 分类: 爬虫集群，协调工作

#### URL 别名
看起来不一样的URL，实际指向同一资源
- 消除别名:
  1. 规范化 URL
  2. 通过了解 Web 服务器的部分配置（服务器是否大小写无关、当前目录下的索引页面配置、主机名和IP地址、虚拟主机）

#### 机器人的 HTTP
***识别请求首部*** :
User-Agent: 发起请求的爬虫名字
From: 爬虫管理者的 Email 地址
Accept: 告诉服务器爬虫想获取的内容
Referer: 提供了当前 URL 的 URL
因为要支持虚拟主机，所以爬虫要支持 Host 首部

#### 拒绝爬虫访问
每个 docroot 都有一个 robots.txt 描述爬虫访问规则文件
***robots 文件格式***
User-Agent:
Disallow:
Allow:
***HTML 的 robot-control 元标签***
< meta name="robots" content="index,follow">
content: noindex, nofollow, index, follow, noarchive, all, none  

***搜索引擎的 meta 标签***
< meta name="description" content="">
< meta name="keywords" content="">
< meta name="revisit-after" content="">

#### 搜索引擎
***全文索引*** :
全文索引就是一个数据库
***对结果排序，并提供查询结果***
相关性排名
