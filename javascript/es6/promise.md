# Promise

## Promise

## 基本思想

是统一管理异步操作和回调的对象，使得异步函数可以用 pipe 的方式来书写，避免回调地狱。

有三种状态：pending、fulfilled、rejected。

* 语法糖
* promise.resolve 是什么
* promise.all promise.race的区别？
* promise 如何实现并发控制

```text
- 并发数量数量 push 到数组里面 用await Promise.all。
```

```text
- 使用 promise 的增强库，例如 bluebird。
```

* 错误处理
* catch 只是 then 的语法糖：obj.catch\(onRejected\) 等同于 obj.then\(undefined, onRejected\)\)
* 因此 catch 返回的依然是一个新的 promise，pending 或 fulfilled 状态。注意，不是拒绝态。
* 原理
* promise 对象中
* 最重要的是 resolvePromise 方法，用于

## 中断链式

## 异常捕获

