---
title: Promise 抽象异步处理
date: 2020-06-13 09:28:09
tags:
---

相关的问题:
1. 了解 Promise 吗？
  Promise 是异步编程的一种解决方案，比传统的异步解决方案 ***回调函数*** 和 ***事件*** 更合理、更强大。现已被 ES6 纳入进规范中。
2. Promise 解决的痛点是什么？
  ```javascript
    step1(function (value1) {
      step2(value1, function(value2) {
        step3(value2, function(value3) {
          step4(value3, function(value4) {
            // Do something with value4
          });
        });
      });
    });
  ```
  解决异步嵌套即回调地狱的问题
  - 回调地狱的负面作用:
    + 代码臃肿可读性差
    + 耦合度高可维护性差
    + 代码复用性差
    + 变量命名不方便，容易产生bug
    + 只能在回调里处理异常
3. Promise 解决的痛点还有其他方法可以解决吗？如果有，请列举
4. Promise 如何使用？
5. Promise 常用的方法有哪些？它们的作用是什么？
6. Promise 在事件循环中的执行过程是怎样的？
7. Promise 的业界实现都有哪些？
  [Q](https://github.com/kriskowal/q): A promise library for JavaScript
  [Bluebird](https://github.com/petkaantonov/bluebird): Bluebird is a fully featured promise library with focus on innovative features and performance
8. 能不能手写一个 Promise 的 polyfill
