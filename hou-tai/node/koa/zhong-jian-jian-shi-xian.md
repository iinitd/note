# 中间件实现

Node

* 多进程
* 多线程
* 进程线程通信
* * * node.js的优化
* egg.js多进程通信机制和架构。顺便聊操作系统进程通信那块。架构我很清楚master，agent，worker的，通信机制没答好，其实看过好几次但没记住。
* 对node.js的理解
* koa看过原理？说说koa-compose咋实现中间件的（就这一个不会，我说了我确实看过和观察者模式有关。加上腾讯怼我的搞得我后来都不敢写熟悉node.js了）
* 服务端渲染有没有了解过
* -express、koa、koa2
* SSR讲一下理解 怎么同构
* node 引入一个模块的过程是什么
* typescript
  * typescript中type和interface的区别
* node中cluster是怎样开启多进程的，并且一个端口可以被多个进程监听吗

## KOA2

### compose咋实现中间件

一个极简的流式 nodejs 服务器框架。

## 中间件原理

app.use

1. app.use\(handler\) 的时候，会有一个队列存储 handler，姑且取名为 stack 吧，每 use 一次就往 statck 中 push。
2. 遍历 handler 队列

```text
function handle(req,res) {
var index=0;
function next(){
var handler = statck[index];
index++;
handler(req,res,next)
}
next()
}
```

数据传递，挂载在 ctx 上。

## koa-router

[https://juejin.im/post/5ba7868e6fb9a05cdf309292](https://juejin.im/post/5ba7868e6fb9a05cdf309292)

## koa-compose

```text
module.exports = compose

function compose (middleware) {

if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')

for (const fn of middleware) {

if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')

}

return function (context, next) {

// last called middleware #

let index = -1

return dispatch(0)

function dispatch (i) {

if (i <= index) return Promise.reject(new Error('next() called multiple times'))

index = i

let fn = middleware[i]

if (i === middleware.length) fn = next

if (!fn) return Promise.resolve()

try {

return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));

} catch (err) {

return Promise.reject(err)

}

}

}

}
```

## 异常处理

定义HttpError和CustomError

在中间件 catch error

app.use\(catchError\) //一定要放在第一个中间件

```text
app.use(async (ctx, next) => {
  try {
    await next();
  } catch (err) {
   if (err instanceof CustomError || err instanceof HttpError){}
   else{
    ctx.status = err.status || 500;
    ctx.body = err.message;
    }
    ctx.app.emit('error', err, ctx);
  }
});

app.on('error', (err, ctx) => {
  /* centralized error handling:
   *   console.log error
   *   write error to log file
   *   save error and request information to database if ctx.request match condition
   *   ...
  */
});

//  if (err instanceof CustomError || err instanceof HttpError)
```

