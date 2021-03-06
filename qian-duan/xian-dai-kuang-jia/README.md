# 现代框架

## 架构模式

mvc mvp mvvm fp frp

前端技术架构的演进，多页-&gt;单页-&gt;微服务（后来知道部门是基础设施架构部门，所以这块问的多一点）

* react和vue的区别
* 单页应用
* 优点
  * 公共资源只需加载一次
* 缺点
  * 不利于 SEO
* 原理
  * js 通过

## 微服务

* iframe的缺点
* 子项目需要改造，需要提供一组不带导航的功能
* iframe嵌入的显示区大小不容易控制，存在一定局限性
* URL的记录完全无效，页面刷新不能够被记忆，刷新会返回首页
* iframe功能之间的跳转是无效的
* iframe的样式显示、兼容性等都具有局限性
* vue怎么实现单页应用
* vue-router构建单页应用
* backbone 跟 vue开发上对比
* Vue
* 数组类型响应式的实现
* 组件注册的几种形式，全局组件如何注册
* vue指令 什么情况下用到
* Vue请求数据的方式

```text
- vue重写了 Array.prototype 上的几个方法，在用这些方法修改数组时可以通知到相关的watcher
```

```text
- 用vue实现一个抽屉组件（宽度屏宽，高度由内容决定，自下而上）
```

* vue源码相关的一些问题，数据驱动层面的实现
* 双向绑定原理，实现
* vue的template是啥，模板引擎原理
* vue和ng对比，和react对比
* vue常见的指令，这个说完指令我说了实现，因为都看过源码
* vue的diff
* 父子组件的created\(\)和computed\(\)的执行先后顺序，为什么
* 异步请求适合在哪个生命周期调用
* vue生命周期，每个中间底层干了什么
* 双向绑定，proxy和defineProperty对比
* vue组件通信怎么实现 父子和不父子
* 插槽
* v-model具体实现数据绑定的步骤
* ：sync修饰符内部实现
* MVVM双向绑定
* vue的响应式原理
* 当你把一个普通的 JavaScript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。render函数访问属性时，会在相应的getter函数中绑定相应的watcher依赖，当属性值改变时，会调用其setter方法，通知各自的watcher，并触发更新函数，生成虚拟dom，利用diff算法，将被更改的部分更新到真实dom中。
* 7.vue2.0和1.0区别

## VDOM & Diff 算法

* VDOM：是一个表示DOM的 javascript 对象。
* diff 算法：深度优先遍历
* walk\(newNode, oldNode, patches, index\)
  * patch type：insert，remove，replace，props，vtext
* 当前节点比对

```text
- 旧不存在，patch type 为 insert
```

```text
- 新不存在，patch type 为 remove
```

```text
- 新旧都是 vnode，且 tag 相同
```

```text
  - 比对 props
```

```text
    - 没有新的，remove_prop
```

```text
    - 有新的，set_prop
```

```text
  - （重点）比对子节点
```

```text
- 新旧都是 vtext，文本不一样时替换文本
```

```text
- 其他情况直接用新的替换旧的，patch type 为 replace
```

* 子节点比对

```text
- cito、snabbdom、
```

```text
- https://juejin.im/post/59bfbd736fb9a00a52065ec7
```

* 优缺点
  * 虚拟DOM就是为了解决这个浏览器性能问题而被设计出来的。例如前面的例子，假如一次操作中有10次更新DOM的动作，虚拟DOM不会立即操作DOM，而是将这10次更新的diff内容保存到本地的一个js对象中，最终将这个js对象一次性attach到DOM树上，通知浏览器去执行绘制工作，这样可以避免大量的无谓的计算量。

## Web Component

* Shadow DOM
* 
## 路由管理

* 14.vue-router实现原理 底层api，不使用vue-router自己怎么实现路由跳转
* 15.单页应用，从一个url跳到另一个url做了什么事情

## 数据可视化

d3.js相关的一些东西

