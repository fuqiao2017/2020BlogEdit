---
title: JS 中的 this
date: 2020-06-28 10:10:40
tags:
---
***关键词*** : 词法作用域   this   箭头函数
bind可以修正SetTimeout和SetInterval的回调函数的this指向
``` javascript
// setTimeout setInterval 回调函数 this 的指向
// 1. this 值分配给封闭变量
function Person() {
  var that = this
  that.age = 1
  setTimeout(function () {
    // that 指向的是实例对象
    that.age++
  }, 1000)
}
var p = new Person()
// 2. 使用 绑定函数
function Person() {
  this.age = 1
  setTimeout(function () {
    // that 指向的是实例对象
    this.age++
  }.bind(this), 1000)
}
var p = new Person()
// 3. 使用 箭头函数：箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this
function Person() {
  this.age = 1
  setTimeout(() => {
    // that 指向的是实例对象
    this.age++
  }, 1000)
}
var p = new Person()
```
**箭头函数的 this 指向当前所在的 function scope (不包括箭头函数)**
``` javascript
// this 其实是在函数域里的值
var obj = {
  a: function () {console.log('this = ', this)}
}
```
由于 箭头函数没有自己的this指针，通过 call() 或 apply() 方法调用一个函数时，只能传递参数(不能绑定this），他们的第一个参数会被忽略。（这种现象对于bind方法同样成立）
箭头函数不绑定Arguments 对象