# 基本思想

在 React16 以前采取的是 Stack Reconciler，任务开始之后无法中断。如果任务耗时过长，就会一直卡住主线程，无法完成浏览器渲染。

因此 Fiber 的核心：

1. 将调和任务拆分成子任务，尽可能利用两个帧之间的执行空闲期来计算。
2. 对任务进行优先级划分，低优先级的通过 requestIdelCallback 执行，高优先级的通过 requestAnimationFrame 执行。

函数调用栈
Fiber




基本单位
函数
Virtual DOM 节点


输入
函数参数
Props


本地状态
本地变量
State


输出
函数返回值
React Element


下级
嵌套函数调用
子节点(child)


上级引用
返回地址
父节点(return)

# 执行过程

## 调和阶段
在这个阶段，计算 DOM 应该如何被更新。

由于在计算的过程中用户是无感知的，因此这个阶段是允许被其他高优先级任务打断的，例如用户的交互操作。但是，打断之后，整个计算需要重新开始。

触发时机：ReactDOM.render() 和 setState。

优先级被划分为 6 种。

* synchronous 与之前的Stack reconciler操作一样，同步执行
* task 在next tick之前执行
* animation 下一帧之前执行
* high 在不久的将来立即执行
* low 稍微延迟（100-200ms）执行也没关系
* offscreen 下一次render时或scroll时才执行

在这个阶段可能调用到的 API 如下。

* componentWillMount
* componentWillReceiveProps
* shouldComponentUpdate
* componentWillUpdate
* render

因为计算可能会重复好几次，因此在 React16 之后，我们应保证这几个 API 是允许被重复调用的。如果存在「假设这个调用只发生一次」的情况，升级框架前就需要更正逻辑了。

通常最容易 componentWillMount 和 componentWillUpdate 这两个 API 中最容易引入副作用。

具体执行过程。

1. 找到根节点优先级最高的workInProgress tree，取其待处理的节点（代表组件或DOM节点）
2. 检查当前节点是否需要更新，不需要的话，直接到4
3. 标记一下（打个tag），更新自己（组件更新`props`，`context`等，DOM节点记下DOM change），并为孩子生成workInProgress node
4. 如果没有产生子节点，归并effect list（包含DOM change）到父级
5. 把孩子或兄弟作为待处理节点，准备进入下一个工作循环。（diff 重点）
6. 如果没有待处理节点（回到了workInProgress tree的根节点），工作循环结束。

### Diff

https://juejin.im/post/5d3e3231e51d4510926a7c39

入口函数 reconcileChildren -> reconcileChildFibers。

代码阅读：https://github.com/crazylxr/deep-in-react/blob/master/analysis/06-rencocilerChildren.md

针对三种不同类型的节点，采取不同的策略。以下说的删除和更新指的是打上 effectTag，等到 commit 节点才会真正执行。

#### TextNode

如果旧的 Fiber 也是 textNode，复用节点，更新文字。

否则，删除节点，创建新的。

#### Element

如果 类型相同且 key 相同，则复用，更新内容，更新ref。

如果类型不相同，则在该节点的子节点中寻找。

#### Array

把老的数组元素都放进map里，遍历新的数组。

找到能复用的则复用，找不到就创建。

## 提交阶段

在这个阶段，完成以下三个事情。

* 更新DOM树
* 
* 调用组件生命周期函数
	* componentDidMount
	* componentDidUpdate
	* componentWillUnmount

* 更新ref等内部状态

由于这个阶段是会被用户感知到的，因此不允许被打断。

正因为不允许被打断，所以在相关的 API 中不应该引入过重的逻辑，以免拉长该阶段的执行时间。

---

尤雨溪在今年的Vue Conf一个观点让我印象深刻：如果我们可以把更新做得足够快的话，理论上就不需要时间分片了。
时间分片并没有降低整体的工作量，该做的还是要做, 因此React 也在考虑利用CPU空闲或者I/O空闲期间做一些预渲染。所以跟尤雨溪说的一样：React Fiber 本质上是为了解决 React 更新低效率的问题，不要期望 Fiber 能给你现有应用带来质的提升, 如果性能问题是自己造成的，自己的锅还是得自己背.


---

http://hcysun.me/vue-design/zh/renderer-diff.html#%E9%9D%9E%E7%90%86%E6%83%B3%E6%83%85%E5%86%B5%E7%9A%84%E5%A4%84%E7%90%86%E6%96%B9%E5%BC%8F

https://juejin.im/post/5dadc6045188255a270a0f85