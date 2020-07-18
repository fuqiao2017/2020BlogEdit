---
title: Promise 抽象异步处理
date: 2020-06-13 09:28:09
tags:
---

#### promise 遵循的一些规范
+ promise 的实现内部必须 catch 注册的所有回调和 executor ***throw*** 的错误(或者其他错误，比如调用一个未定义的函数)，然后把 promise 的状态改变到 rejected，(即吃掉抛出的错误)；此时错误需要被后续的 .catch 方法注册的回调处理，或者被 .then 方法注册的 onRejected 方法处理，否则会报 uncaught 错误，即错误继续被抛出
``` javascript
// 外层 try catch 不能捕获 promise 代码里 throw 的错误
try {
  // 内部的实现: 把 ReferenceError: aaaaa is not defined catch 后 调用 reject 方法改变 promise 的状态，后续没有处理，所以报 uncaught 错误
  new Promise((resolve, reject) => {aaaaa()})
} catch (err) {
  console.log('catch nothing')
}
```
<!--more-->

***三个状态*** : 状态不可逆
pending: 初始状态
fulfilled: 成功状态
rejected: 失败状态

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
  ES6 出现了 generator 以及 async/await 语法，使异步处理更加接近同步代码写法，可读性更好
4. Promise 如何使用？
  + new Promise(fn)
  + Promise.resolve(fn)
5. Promise 常用的方法有哪些？它们的作用是什么？
  类方法:
  Promise.all()
  Promise.race()
  Promise.reject()
  Promise.resolve()
  实例方法:
  Promise.prototype.catch()
  Promise.prototype.finally()
  ***Promise.prototype.then()*** :
  onFulfilled: 该参数预期是一个函数，如果参数不是函数，则会在内部被替换为 (x) => x，并且会发生 ***值穿透***
6. Promise 在事件循环中的执行过程是怎样的？
  new Promise( function(resolve, reject) {...} /* executor */  )
  Promise构造函数执行时立即调用executor 函数， resolve 和 reject 两个函数作为参数传递给executor；
  executor 内部通常会执行一些异步操作，一旦异步操作执行完毕(可能成功/失败)，就会调用对应的函数修改 promise 的状态；
  .then 方法是一个微任务，promise 的 fulfilled 和 rejected 状态会触发对应的回调函数进入 Event queue 等待进栈执行
7. Promise 的业界实现都有哪些？
  [Q](https://github.com/kriskowal/q): A promise library for JavaScript
  [Bluebird](https://github.com/petkaantonov/bluebird): Bluebird is a fully featured promise library with focus on innovative features and performance
8. 能不能手写一个 Promise 的 polyfill


##### Promise 题目
``` javascript
// 题目一
Promise.resolve()
  .then(() => {
    // new Error('') 只是一个对象，真正 throw 才是抛出错误
    // 返回任意一个非 promise 的值都会被包裹成 promise 对象
    return new Error('error!!!') // 等价于 return Promise.resolve(new Error('error!!!'))
    // 正确写法，可以被 catch
    return Promise.reject(new Error('error!!!'))
    throw new Error('error!!!')
  })
  .then((res) => {
    console.log('then: ', res)
  })
  .catch((err) => {
    console.log('catch: ', err)
  })
```

``` javascript
// 题目二
const promise = Promise.resolve()
  .then(() => {
    // .then 或 .catch 返回的值不能是 promise 本身，否则会造成死循环
    return promise
  })
  // TypeError: Chaining cycle detected for promise #<Promise>
promise.catch(console.error)
```

``` javascript
// 题目三
Promise.resolve(1)
  .then(2)
  .then(Promise.resolve(3)) // 这里如果是 Promise.reject('errored')，因为不是函数，依然会被忽略，但是会产生一个不能被捕获的error
  .then(console.log)
  // .then 或者 .catch 的参数期望是函数，传入非函数则会发生值穿透
  // If onFulfilled is not a function and promise1 is fulfilled, promise2 must be fulfilled with the same value as promise1
  // 结果 1
```

``` javascript
// 题目四
Promise.resolve()
  .then(function success (res) {
    throw new Error('error')
  }, function fail1 (e) {
    console.error('fail1: ', e)
  })
  .catch(function fail2 (e) {
    console.error('fail2: ', e)
  })
// 等价于
Promise.resolve()
  .then(function success1 (res) {
    throw new Error('error')
  }, function fail1 (e) {
    console.error('fail1: ', e)
  })
  .then(function success2 (res) {
  }, function fail2 (e) {
    console.error('fail2: ', e)
  })
// 执行结果都是 fail2: Error
```

``` javascript
// 题目五
process.nextTick(() => {
  console.log('nextTick')
})
Promise.resolve()
  .then(() => {
    console.log('then')
  })
setImmediate(() => {
  console.log('setImmediate')
})
console.log('end')
// 结果 主线程宏任务 -> 微任务(优先执行 process.nextTick) -> 下一个宏任务(优先执行 setImmediate)
// end
// nextTick
// then
// setImmediate
```

``` javascript
// rejected 状态如果通过 catch 方法捕获后(或者接着 then 方法的第二个参数)，状态会变为 resolved
Promise.resolve().then(function() {
  return Promise.reject('errored')
}) // 一个 promise rejected: errored
Promise.resolve().then(function() {
  return Promise.reject('errored')
}).catch(function(err) {
  console.log(err)
}) // 一个 promise resolved: undefined
// 等价
Promise.resolve().then(function() {
  return Promise.reject('errored')
}).then(function () {}, function(err) {
  console.log(err)
}) // 一个 promise resolved: undefined
```
