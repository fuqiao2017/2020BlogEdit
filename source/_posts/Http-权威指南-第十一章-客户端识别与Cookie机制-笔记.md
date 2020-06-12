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
第三方网站引导发出的 Cookie，就称为第三方 Cookie。它除了用于 CSRF 攻击，还可以用于用户追踪。

#### cookie 与缓存
私有的内容不可缓存


#### cookie 安全问题
- cookie 相关特性: http 请求会自动携带请求所在域名的 cookie

- CSRF (XSRF) (Cross-site request forgery) 跨站请求伪造
  也被称为 ***one-click attack***
  栗子: 小明刚刚在360浏览器登陆过中国银行网站，登录状态还有效，这时小明在逛一个有趣的论坛，小明在浏览一个有趣的帖子，帖子里有个人回复了留言和一个外链([点我看美女](http://www.chinabank.com/transfer?from=xiaoming&amount=10000&to=heike))，那么小明点击，浏览器就会发出一个携带 www.chinabank.com 域名下的 cookie 的请求，Refer 可能是当前这个论坛的域，所以小明损失一万块
  或者: 一个钓鱼网站有向中国银行转账接口提交的表单，诱发用户点击按钮，就会携带 cookie 提交表单
***防御措施*** :
1. 检查 Referer 字段
  此方法完全依赖客户端浏览器发送正确的值
2. 添加校验 token
  登录后服务器返回一个 token，前端把这个 token 添加到 http 请求的首部，攻击者无从事先得知这个 token 的值
3. SameSite 属性
  Set-Cookie: CookieName=CookieValue; SameSite=Strict;
  Strict: 只有当前网页的 URL 与请求目标一致，才会带上 Cookie
  Lax: (默认) 大多数情况也是不发送第三方 Cookie，但是导航到目标网址的 Get 请求除外 (导航到目标网址的 GET 请求，只包括三种情况：链接，预加载请求，GET 表单)
  None: 值为 None 时，必须同时设置 Secure 属性，表示 cookie 只能通过 HTTPS 发送，Set-Cookie: widget_session=abc123; SameSite=None; Secure
4. 表单嵌入同步令牌（一个随机唯一的 token）


- XSS (Cross-site scripting  XSS) 跨站脚本
  跨站脚本是代码注入的一种
  ***攻击手段和目的*** : 获取 cookie，获取本地存储的用户敏感信息，其他操作
  ***防御措施*** :
  1. 过滤特殊字符
    客户端和服务器端都对增加正则校验，没有通过校验则客户端不允许提交，服务器端不允许入库
  2. 数据转义
    前端把需要展示到页面的数据转义，比如直接 replace script 标签，遇到 “<” 或 “>” 添加 “\” 转义处理
  3. 设置 http-only 属性
    cookie 有一个 http-only 属性，表示 cookie 只能被 http 请求携带，不能通过 document.cookie 获取

***cookie 会导致频繁发送 首部体积庞大 的请求***

构建用户跟踪: 将持久 cookie 与 IP 地址、Referer 结合可以构建相当精确的用户档案和浏览模式信息


