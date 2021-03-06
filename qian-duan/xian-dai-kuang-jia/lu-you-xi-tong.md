# 路由系统

## 基本概念

* 核心：hash、history
* hash
* 将 path及其对应的回调 注册到自定义的路由对象中
* 实现路由对象的dispatch功能
* 将其绑定load和hashchange事件

history API

* pushState
* replaceState

## 基本实现

* 将 path及其对应的回调 注册到自定义的路由对象中
* 实现路由对象的dispatch功能
* 为实现软路由的 a 标签添加 data-href 自定义属性
* 获取所有软路由，绑定 click事件：禁止默认行为，在回调中触发dispatch
* 将dispatch绑定到load和popstate事件
* vue和react的实现：[https://juejin.im/entry/5a2cbd51f265da430b7b2b82](https://juejin.im/entry/5a2cbd51f265da430b7b2b82)

## API

hash ：hash 虽然出现在 URL 中，但不会被包含在 http 请求中，对后端完全没有影响，因此改变 hash 不会重新加载页面。

hash 模式的原理是 onhashchange 事件，可以在 window 对象上监听这个事件。

history ：history 利用了 html5 history interface 中新增的 pushState\(\) 和 replaceState\(\) 方法。这两个方法应用于浏览器记录栈，在当前已有的 back、forward、go 基础之上，它们提供了对历史记录修改的功能。只是当它们执行修改时，虽然改变了当前的 URL ，但浏览器不会立即向后端发送请求。

history ：hashchange 只能改变 \# 后面的代码片段，history api （pushState、replaceState、go、back、forward） 则给了前端完全的自由，通过在window对象上监听popState\(\)事件。

