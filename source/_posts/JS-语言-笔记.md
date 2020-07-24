---
title: JS 语言 笔记
date: 2020-07-23 17:19:06
tags:
---

1. new 是返回对象，没有 new 是执行函数
```javascript
function MyFunc() {
  this.a = 1
  return this
}
console.log(MyFunc()) // Window {}
console.log(new MyFunc()) // MyFunc {a: 1}
```