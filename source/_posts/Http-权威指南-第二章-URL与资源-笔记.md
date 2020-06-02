---
title: Http 权威指南 第二章 URL与资源 笔记
date: 2020-06-02 14:09:10
tags:
---

## URL与资源
#### 绝对URL 与 相对URL
1. 相对URL转换为绝对URL算法
2. 浏览器自动扩展URL:
    - 主机名扩展  
      taobao -> www.taobao.com
    - 历史扩展
      匹配历史记录  

#### URL字符集
特点: 完整性、可移植性  
通过转义表示法来表示不安全的字符以及扩展URL字符集

#### 方案的世界
1. HTTPS: https 使用了网景的 SSL (Secure Sockets Layer 安全套接字协议: 为 Http 的连接提供了端到端的加密机制，默认端口 443) 或 TLS (Transport Layer Security 传输层安全协议)
2. RTSP, RTSPU: RTSP URL 是可以通过 实时流传输协议 (Real Time Streaming Protocol) 解析的音视频媒体资源的标识符  
RTSPU 中的 U 表示使用 UDP 协议获取资源