---
title: 自己实现一个Promise类
date: 2018-07-10 23:29:26
tags: javascript
---

## 思考

最开始实现的时候总是把焦点放在函数上面，后面才发现应该将焦点放在所得到的结果，核心思想就是把`resolve`的值保存下来，然后执行`then`所传入的回调函数时，将值传入，`reject`同理。

对于异步的结果，当状态为`pending时将onFulfilled`函数保存，当函数执行`resolve`的时候再执行`onFulfilled`并把值传入。

<!-- more -->

## 实现

```javascript
function Promise(executor) {
  this.state = 'pending'
  this.value = undefined
  this.reason = undefined
  this.onResolveAsyncCallbacks = []
  this.onRejectAsyncCallbacks = []
  const resolve = (value) => {
    if (this.state === 'pending') {
      this.state = 'fulfilled'
      this.value = value // 保存结果
      this.onResolveAsyncCallbacks.forEach(fn => fn(value)) // 执行异步回调
    }
  }
  const reject = (reason) => {
    if (this.state === 'pending') {
      this.state = 'rejected'
      this.reason = reason
      this.onRejectAsyncCallbacks.forEach(fn => fn(reason))
    }
  }
  try {
    executor(resolve, reject)
  } catch (err) {
    reject(err)
  }
}

Promise.prototype.then = function (onFulfilled) {
  if (this.state === 'fulfilled') {
    const result = onFulfilled(this.value)
    // 如果结果是一个Promise对象则返回结果，实现链式调用
    if (result && result.constructor === Promise) {
      return result
    }
  }
  if (this.state === 'pending') {
    // this.onResolveAsyncCallbacks.push(onFulfilled)
    // 返回Promise 避免异步返回 this，实现链式调用
    return new Promise((resolve) => {
      this.onResolveAsyncCallbacks.push(() => {
        const result = onFulfilled(this.value)
        if (isObject(result) && result.constructor.name === 'Promise') {
          return result.then(r => resolve(r))
        }
        resolve(result)
      })
    })
  }
  return this
}

Promise.prototype.catch = function (onRejected) {
  if (this.state === 'rejected') {
    onRejected(this.reason)
  }
  if (this.state === 'pending') {
    this.onRejectAsyncCallbacks.push(onRejected)
  }
  return this
}

Promise.resolve = function(value) {
  return new Promise((resolve) => {
    resolve(value)
  })
}
Promise.reject = function(value) {
  return new Promise((resolve, reject) => {
    reject(value)
  })
}

```

