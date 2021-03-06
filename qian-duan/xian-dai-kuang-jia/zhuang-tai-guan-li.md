# 状态管理

## 数据流

* 脏检查
* react
* 双向绑定
* 数据到界面的绑定
* vue：将界面更新的回调绑定到数据的setter
* angular：脏检查
  * watch：注册数据和回调
  * digest：遍历watcher，标记dirty，渲染界面
* 状态管理

## redux

Redux 是 JavaScript 状态容器，提供可预测化的状态管理

* 讲一下？有没有用过mobx、dva这样的库？他们跟redux有什么区别？redux的痛点？
* store（Redux 应用只有一个单一的 store）
* 维持应用的 state；
* 提供 getState\(\) 方法获取 state；
* 提供 dispatch\(action\) 方法更新 state；
* 通过 subscribe\(listener\) 注册监听器；
* 通过 subscribe\(listener\) 返回的函数注销监听器
* action
* 把数据从应用传到 store 的有效载荷。它是 store 数据的唯一来源，一般通过 store.dispatch\(\) 将 action 传到 store。
* reduce
* 指定了应用状态的变化如何响应 actions 并发送到 store ，store收到action后，需要给出一个新的状态来更新页面，这种state的计算过程就叫做reducer。

## vuex

* mutation、action、moudle
* vuex的mutation和action的区别，为什么要设立两个而不是直接都用mutation操作state
* vuex plugin用过吗
* Vuex 的 store 接受 plugins 选项，这个选项暴露出每次 mutation 的钩子。Vuex 插件就是一个函数，它接收 store 作为唯一参数
* vuex mutation、action、moudle
* vuex怎么做数据管理的

Vuex、Flux、Redux、Redux-saga、Dva、MobX

## mobx

mobx 的哲学是「任何源自应用状态的东西都应该自动地获得」。

它以一种无障碍、同步、可预见和高效的方式实现了透明的函数响应式编程\(Transparent Functional Reactive Programming\)。

mobx的流程图如上，通常是：触发action，在action中修改state，通过computed拿到state的计算值，自动触发对应的reactions，这里包含autorun，渲染视图等。有一点需要注意：相对于react来说，mobx没有一个全局的状态树，状态分散在各个独立的store中。mobx的工作原理非常简单，使用Object.defineProperty来拦截对数据的访问，一旦值发生变化，将会调用react的render方法来实现重新渲染视图的功能或者触发autorun等。

```text
import * as React from 'react';
import { observable, action, autorun, when, computed } from 'mobx';
import { observer } from 'mobx-react';

@observer
export default class Item extends React.Component {
    disposer: () => void;

    @observable
    count: string = 'right';

    @action
    setCount = () => {
        this.count = this.count === 'right' ? 'wrong' : 'right';
    }

    cancelAutoRun = () => {
        if (this.disposer) {
            this.disposer();
        }
    }

    @computed
    get helloCount() {
        return `hello ${this.count}`;
    }

    render() {
        return(
            <div>
            <h3>{this.count}</h3>
            <h4>{this.helloCount}</h4>
            <button onClick={this.setCount}>click</button>
            <button onClick={this.cancelAutoRun}>cancel</button>
            </div>);
    }

    componentDidMount() {
        this.disposer = autorun(() => {
            console.log(this.count);
        });

        when(
            () => this.count === 'wrong',
            () => console.log(this.count)
        );
    }

}
```

### 概念

Observable 是一个装饰器。为现有数据结构提供「可观察」的功能。

```text
import { observable } from "mobx";

class Todo {
    id = Math.random();
    @observable title = "";
    @observable finished = false;
}
```

Computed 是一个装饰器。追踪 observables 的变化，实时反映在「计算值」上。

```text
class TodoList {
    @observable todos = [];
    @computed get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length;
    }
}
```

Reactions 是一个概念，和计算值很像，追踪 observables 的变化，实时产生副作用。

Actions 提供类 Flux 的触发事件、调用分派程序或者类似的工作。

### Reaction

Autorun

```text
autorun(() => {
    console.log("Tasks left: " + todos.unfinishedTodoCount)
})
```

Reaction 提供比 autorun 更细粒度的追踪。

```text
const todos = observable([
    {
        title: "Make coffee",
        done: true,
    },
    {
        title: "Find biscuit",
        done: false
    }
]);

// reaction 的错误用法: 对 length 的变化作出反应, 而不是 title 的变化!
const reaction1 = reaction(
    () => todos.length,
    length => console.log("reaction 1:", todos.map(todo => todo.title).join(", "))
);

// reaction 的正确用法: 对 length 和 title 的变化作出反应
const reaction2 = reaction(
    () => todos.map(todo => todo.title),
    titles => console.log("reaction 2:", titles.join(", "))
);

// autorun 对它函数中使用的任何东西作出反应
const autorun1 = autorun(
    () => console.log("autorun 1:", todos.map(todo => todo.title).join(", "))
);

todos.push({ title: "explain reactions", done: false });
// 输出:
// reaction 1: Make coffee, find biscuit, explain reactions
// reaction 2: Make coffee, find biscuit, explain reactions
// autorun 1: Make coffee, find biscuit, explain reactions

todos[0].title = "Make tea"
// 输出:
// reaction 2: Make tea, find biscuit, explain reactions
// autorun 1: Make tea, find biscuit, explain reactions
```

**When** 追踪 predicate，直到其为 true，执行 effect。

```text
class MyResource {
    constructor() {
        when(
            // 一旦...
            () => !this.isVisible,
            // ... 然后
            () => this.dispose()
        );
    }

    @computed get isVisible() {
        // 标识此项是否可见
    }

    dispose() {
        // 清理
    }
}
```

### 原理

「MobX 会对在**追踪函数**执行**过程**中**读取**现存的可观察属性做出反应。」

* 读取：是对象属性的间接引用。可以用过 . \(例如 [user.name](http://user.name/)\) 或者 \[\] \(例如 user\['name'\]\) 的形式完成。
* 追踪函数：computed 表达式、observer 组件的 render\(\) 方法、 when \| reaction \| autorun 的第一个入参函数。
* 过程：只追踪那些在函数执行时被**读取**的 observable 。这些值是否由追踪函数直接或间接使用并不重要。

MobX 不会对其作出反应:

* 从 observable 获取的值，但是在追踪函数之外
* 在异步调用的代码块中读取的 observable

### 引用

**「现在 MobX 基本上所做的是记录你在函数中使用的是哪个箭头。之后，只要这些箭头中的其中一个改变了\(它们开始引用别的东西了\)，它就会重新运行。」**

## React provider connect

