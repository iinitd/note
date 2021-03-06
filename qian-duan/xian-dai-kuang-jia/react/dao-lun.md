# 导论

怎么使用 Hooks 替代高阶组件？

#### 基本概念

React 是什么？

React 是一个数据驱动的UI库。核心是在内存中维护一个虚拟 DOM 树，在数据变化时，将新老两棵树进行调和（reconcilation/diff），形成 patch 列表，然后更新 DOM。

#### 组件

本质：一个函数，接收 props，返回可渲染的 react 元素。

分类

* 函数组件
* class组件

设计模式

* 核心b：职责单一化
* 容器组件
* 高阶组件
* render callback

fc和cc的区别

* 语法：FC直接返回jsx。CC继承自React.component，在render里面返回jsx。
* 状态：FC无状态，不能使用setState。CC有状态。
* 生命周期：FC无生命周期，有HOOK。CC有生命周期。

#### 生命周期

[https://juejin.im/post/5d3db4da518825016f644561](https://juejin.im/post/5d3db4da518825016f644561)

* 15（旧版）
* 挂载
* componentWillMount
* render
* componentDidMount
* componentWillUnmount
* 更新
* componentWillReceiveProps
* shouldComponentUpdate\(nextProps, nextState\)
* componentWillUpdate
* render
* componentDidUpdate
* 洋葱模型
  * 父WillMount-子willmount-render-子didmount-父didmount
* 16（新版）
* 挂载
* getDerivedStateFromProps
* render
* componentDidMount
* 更新
* getDerivedStateFromProps
* shouldComponentUpdate
* render
* getSnapshotBeforeUpdate
* 多个组件的执行顺序
* 挂载：
  * 父组件 getDerivedStateFromProps —&gt; 同步子组件 getDerivedStateFromProps —&gt; 同步子组件 componentDidMount —&gt; 父组件 componentDidMount —&gt; 异步子组件 getDerivedStateFromProps —&gt; 异步子组件 componentDidMount
* 更新
  * 父组件 getDerivedStateFromProps —&gt; 父组件 shouldComponentUpdate —&gt; 子组件 getDerivedStateFromProps —&gt; 子组件 shouldComponentUpdate —&gt; 子组件 getSnapshotBeforeUpdate —&gt; 父组件 getSnapshotBeforeUpdate —&gt; 子组件 componentDidUpdate —&gt; 父组件 componentDidUpdate

### 总结

挂载阶段，

少了 ComponentWillMount\(\) 多了getDerivedStateFromProps\(\)

更新阶段

没了ComponentWillUpdate 多了getSnapshotBeforeUpdate

#### setState

#### react diff过程

* key的作用

## 事件

```text
<button onCLick = {this.a) class="j_button"} >
a(event){
     event.stopPropagation()
}

$(document).on('click','.j_button', () => {alert(1)})
```

react只是在最外层的document进行监听

而且react中的onClick 传递的event,是react的合成event

所以event.stopPropagation\(\) 只会阻止react自己定义的事件冒泡

event.stopPropagation：在react事件回调函数中调用能阻止react合成事件的冒泡 event.nativeEvent.stopPropagation：基本无作用, 阻止的是react的根元素\(例如body\)的冒泡\(不能阻止代理到body的事件\)。body可能冒泡给谁?.document,window event.nativeEvent.stopImmediatePropagation: 能阻止代理到body的事件

## ref

存储 DOM 节点的引用。

