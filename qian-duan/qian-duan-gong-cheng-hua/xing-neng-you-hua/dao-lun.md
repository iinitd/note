# 导论

前端工程

## 模块化

* 模块化
* [https://juejin.im/post/5e9d963351882573ab451576](https://juejin.im/post/5e9d963351882573ab451576)
* \[[https://es6.ruanyifeng.com/\#docs/module-loader\#CommonJS-%E6%A8%A1%E5%9D%97%E7%9A%84%E5%8A%A0%E8%BD%BD%E5%8E%9F%E7%90%86\]\(\(null](https://es6.ruanyifeng.com/#docs/module-loader#CommonJS-%E6%A8%A1%E5%9D%97%E7%9A%84%E5%8A%A0%E8%BD%BD%E5%8E%9F%E7%90%86]%28%28null)\)\)
* commonJS、AMD、CMD
* AMD

```text
- 异步模块定义
```

```text
- 依赖前置
```

```text
- 一个API可以多用
```

```text
- define（id，dep，cb）
```

* CMD

```text
- 通用模块定义
```

```text
- 依赖就近
```

```text
- API职责单一
```

* commonjs

```text
- 每个 js 文件都是独立的模块上下文
```

```text
- 在上下文里面创建的属性都是私有的
```

```text
- 同步加载模块，不适用于浏览器
```

* es6 module

```text
- export
```

```text
- 可以导出类、方法
```

* seajs跟requirejs对比
* CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
* CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
* CommonJs 是单个值导出，ES6 Module可以导出多个
* CommonJs 是动态语法可以写在判断里，ES6 Module 静态语法只能写在顶层
* CommonJs 的 this 是当前模块，ES6 Module的 this 是 undefined

## Babel

### 基本概念

plugin

preset

polyfill：babel 默认只转译语法，不转换新的API和对象。需要借助 babel-polyfill。

babel-runtime：包括core-js和 regenerator runtime。

~~bbrt~~

### 工作流程

1. 解析：词法分析、语法分析，生成 AST
2. 转换：深度优先遍历，通过 visitor 操作 AST。
3. 生成：根据新的 AST 输出代码。

## 前端监控

* * 灰度
* AB测试
* 前端监控
* 页面监控错误，错误处理和性能信息，onerror用法
* 一个url接口数组 页面依赖这些接口数据拼装渲染，怎么做，如果其中有接口有可能失败怎么办
* 前端监控需要关注哪些数据
* 超时
* 首屏加载时间
* 白屏时间
* 异常捕获：[https://www.html.cn/archives/5014](https://www.html.cn/archives/5014)
* 页面崩溃
  * 基于service worker 心跳统计
* 前端性能优化
* 前端优化

```text
- https://juejin.im/post/5b94d8965188255c5a0cdc02
```

```text
- 减少 iframe 数量
```

```text
- 减少 http 请求数量
```

```text
- 提前加载
```

```text
- 减少http请求；
```

```text
- 合并脚本文件；
```

```text
- 指定Expires或者Cache-Control；
```

```text
- 缓存ajax请求；
```

```text
- 移除重复的js;
```

```text
- 避免重定向；
```

```text
- 使用CDN；
```

```text
- 减小返回数据的体积（可使用gzip压缩返回数据，最小化js文件和css文件，将css和js独立成外部文件）；
```

```text
- 优化cookie(减小cookie的体积，合理设置Cookie域，设置合理的cookie过期时间，使用域分离)
```

```text
- 优化浏览器加载（将css放在页面顶部加载，js放在页面底部加载）
```

* 节流和防抖
* 前端性能优化，那些可以实现自动化
* 页面加载海量数据时滑动会卡顿如何解决
* 懒加载、css硬件加速、节点回收、减少dom层级嵌套、
* 首屏加载优化怎么做
* 一个页面上有大量的图片，加载很慢，你有哪些方法优化这些图片的加载？
* 一次js请求一般情况下有哪些地方会有缓存处理？
* 聊天界面中，每发送一个消息页面会自动上移，当好多条消息并发出来时页面会卡顿，如何解决？
* [https://juejin.im/post/5e9052916fb9a03c9843284f](https://juejin.im/post/5e9052916fb9a03c9843284f)

## 前端性能优化

seo

首屏

C侧关注点

## 前端灰度与上线机制

## 联调

dev-server

mock 数据

