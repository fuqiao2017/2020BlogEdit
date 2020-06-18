---
title: Promise 实现一个promise
date: 2020-06-15 17:24:44
tags:
---

#### Promise/A+ 规范概要
1. 三个状态: pending, fulfilled, rejected
2. 状态一旦由 pending 改变，就不在变化

``` javascript
// es5 实现
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
function MyPromise(executor) {
  var self = this
  // 状态
  self.status = PENDING
  // value 成功状态的值，js 合法值，包括 undefined, thenable, promise
  self.value = undefined
  // reason 异常状态的原因短语
  self.reason = undefined
  // 两个数组分别保存 then 方法中注册的回调函数
  self.onFulfilledCbs = []
  self.onRejectedCbs = []
  function _resolve(value) {
    if (self.status === PENDING) {
      self.status = FULFILLED
      self.value = value
      // 依次执行 注册的 onfulfilled 回调函数
      // 如果某个 fulfilled 函数里返回一个 rejected promise 呢？
      self.onFulfilledCbs.forEach(callback => {
        callback(self.value)
      })
    }
  }
  function _reject(reason) {
    if (self.status === PENDING) {
      self.status = REJECTED
      self.reason = reason
      // 依次执行 注册的 onrejected 回调函数
      self.onRejectedCbs.forEach(callback => {
        callback(self.reason)
      })
    }
  }
  try {
    executor(_resolve, _reject)
  } catch (err) {
    _reject(err)
  }
}

MyPromise.prototype = {
  constructor: MyPromise,
  // then 方法参数期望函数，如果不是，则 ignore 参数并接受上一步 promise 的值
  then: function (onFulfilled, onRejected) {
    var realOnFulfilled = onFulfilled
    if (typeof realOnFulfilled !== 'function') {
      realOnFulfilled = function (value) {
        return value
      }
    }
    var realOnRejected = onRejected
    if (typeof realOnRejected !== 'function') {
      realOnRejected = function (reaon) {
        throw reason
      }
    }

    // 如果状态不是 pending，执行对应状态的回调
    if (this.status === FULFILLED) {
      realOnFulfilled(this.value)
    }
    if (this.status === REJECTED) {
      realOnRejected(this.reason)
    }
    if (this.status === PENDING) {
      this.onFulfilledCbs.push(realOnFulfilled)
      this.onRejectedCbs.push(realOnRejected)
    }
  }
}
```
