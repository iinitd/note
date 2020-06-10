# 宏观差异

Vue

Vue1.x 没有vdom，完全的响应式，每个数据变化，都通过响应式通知机制来新建Watcher干活，就像独立团规模小的时候，每个战士入伍和升职，都主动通知咱老李，管理方便

Vue2引入vdom，控制了颗粒度，组件层面走watcher通知， 组件内部走vdom做diff，既不会有太多watcher，也不会让vdom的规模过大，diff超过16ms，真是优秀啊 就像独立团大了以后，只有营长排长级别的变动，才会通知老李，内部的自己diff管理了

Vue3通过Proxy响应式+组件内部vdom+静态标记，把任务颗粒度控制的足够细致，所以也不太需要time-slice了

React走了另外一条路，既然主要问题是diff导致卡顿，于是React走了类似cpu调度的逻辑，把vdom这棵树，微观变成了链表，利用浏览器的空闲时间来做diff，如果超过了16ms，有动画或者用户交互的任务，就把主进程控制权还给浏览器，等空闲了继续，特别像等待女神的备胎

Snabbdom

我们先遍历两个数组\(while语句\),维护四个变量

遍历oldCh的头索引 - oldStartIdx 遍历oldCh的尾索引 - oldEndIdx 遍历newCh的头索引 - newStartIdx 遍历newCh的尾索引 - newEndIdx 当oldStartIdx &gt; oldEndIdx或者newStartIdx &gt; newOldStartIdx的时候停止遍历。

遍历过程中有五种比较 前四种比较

oldStartVnode和newStartVnode,两者elm相对位置不变,若值得\(sameVnode\)比较,这patch这两个vnode oldEndVnode和newEndVnode,同上,elm相对位置不变,做相同patch检测 oldStartVnode和newEndVnode,如果oldStartVnode和newEndVnode值得比较,说明oldCh中的这- - oldStartVnode.elm向右移动了。那么执行api.insertBefore\(parentElm,oldStartVnode.elm, api.nextSibling\(oldEndVnode.elm\)\)调整它的位置 oldEndVnode和newStartVnode,同上,但这是oldVnode.elm向左移,需要调整它的位置 最后一种比较

利用vnode.key,在ul&gt;li\*n的结构里,我们很有可能使用key来标志li的唯一性,那么我们就会来到最后一种情况。这个时候,我们先产生一个index-key表\(createKeyToOldIdx\),然后根据这个表来进行更改。

更改规则

如果newVnode.key不在表中,那么这个newVnode就是新的vnode,将其插入 如果newVnode.key在表中,那么对应的oldVnode存在,我们需要patch这两个vnode,并在patch之后,将这个oldVnode置为undefined\(oldCh\[idxInOld\] = undefined\),同时将oldVnode.elm位置变换到当前oldStartIdx之前,以免影响接下来的遍历

遍历结束后,检查四个变量,对移除剩余的oldCh或添加剩余的newCh

