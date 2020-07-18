---
title: Promise 实现一个promise
date: 2020-06-15 17:24:44
tags:
---

#### Promise/A+ 规范概要
1. 三个状态: pending, fulfilled, rejected
2. 状态一旦由 pending 改变，就不在变化
<!--more-->

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
    // 这里用 try catch 是因为如果是 promise 内部 throw 的 error，规范需要 reject 去"捕获"这个错误
    // 比如 executor 内部同步的代码 throw 了一个 error，这时调用 reject 方法把 promise 状态修改为 rejected
    // 有异步代码需要抛错误，需要手动调用 reject 方法
    executor(_resolve, _reject)
  } catch (err) {
    _reject(err)
  }
}

MyPromise.prototype = {
  constructor: MyPromise,
  // then 方法参数期望函数，如果不是，则 ignore 参数并接受上一步 promise 的值
  then: function (onFulfilled, onRejected) {
    var self = this
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
    if (self.status === FULFILLED) {
      var promise2 = new MyPromise(function(resolve, reject) {
        // 2.2.7.2 If either onFulfilled or onRejected throws an exception e, promise2 must be rejected with e as the reason. 通过 try catch
        try {
          // 2.2.7.3 If onFulfilled is not a function and promise1 is fulfilled, promise2 must be fulfilled with the same value as promise1. 不是函数，则状态传递、值穿透
          if (typeof onFulfilled !== 'function') {
            resolve(self.value)
          } else {
            // promise resolution procedure
            var x = realOnFulfilled(self.value)
            dealMyPromise(promise2, x, resolve, reject)
          }
        } catch(error) {
          reject(error)
        }
      })
      return promise2
    }
    if (self.status === REJECTED) {
      var promise2 = new MyPromise(function(resolve, reject) {
        try {
          // 如果 onrejected 回调不是函数，则直接 reject,
          // 2.2.7.4 If onRejected is not a function and promise1 is rejected, promise2 must be rejected with the same reason as promise1. 不是函数，则状态传递、值穿透
          if (typeof onRejected !== 'function') {
            reject(self.reason)
          } else {
            // 如果是函数则执行函数后 promise resolved
            realOnRejected(self.reason)
            resolve()
          }
        } catch(error) {
          reject(error)
        }
      })
    }
    if (self.status === PENDING) {
      var promise2 = new MyPromise(function(resolve, reject) {
        self.onFulfilledCbs.push(function() {
          try {
            realOnFulfilled(self.value)
          } catch(error) {
            reject(error)
          }
        })
        self.onRejectedCbs.push(function() {
          try {
            realOnRejected(self.reason)
          } catch(error) {
            reject(error)
          }
        })
      })
    }
  }
}

// promise resolution procedure
function dealMyPromise(promise, x, resolve, reject) {
  // 2.3.1 如果 promise 和 x 指向同一对象，以 TypeError 为据因拒绝执行 promise，避免无限循环
  if (promise === x) {
    return reject(new TypeError('The promise and the return value are the same'))
  }

  // 2.3.2 如果 x 是一个 promise，则 promise 接受 x 的状态
  // 是一个 promise 继续往下执行
  if (x instanceof MyPromise) {
    x.then(function(y) {
      resolveMyPromise(promise, y, resolve, reject)
    }, reject)
  }
  // 2.3.3 如果 x 为对象或者函数
  else if (typeof x === 'object' || typeof === 'function') {
    // 如果 x 是 null, 直接 resolve
    if (x === null) {
      return resolve(x)
    }
    // 2.3.3.2 If retrieving the property x.then results in a thrown exception e, reject promise with e as the reason. 如果检索 x 的 then 属性抛出异常则以这个异常为原因 reject
    try {
      var then = x.then
    } catch (error) {
      return reject(error)
    }
    // 2.3.3.3 如果 then 是函数
    if (typeof then === 'function') {
      // 将 x 作为函数的作用域 this 调用之
      // 传递两个回调函数作为参数，第一个参数为 resolvePromise, 第二个参数为 rejectPromise
      try {
        then.call(
          x,
          // 2.3.3.3.3 如果 resolvePromise 和 rejectPromise 均被调用
          // 或者被同一参数调用了多次，则优先采用首次调用并忽略剩下的调用
          function () {

          }
        )
      }
    }
  }
}
```
