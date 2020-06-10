# 事件循环

## 基本思想

是在宿主环境里面实现的一个异步机制，是用来解决对 JavaScript 引擎单线程的同步阻塞问题。

他本质上是通过宿主环境 Runtime 提供的非阻塞 API，将任务的等待或计算移出主线程的调用栈，由其他线程完成，同时把任务放入任务队列，当调用栈为空时检查任务队列，取出最早的可执行任务调回主线程。

根据任务的优先级，将任务划分为宏任务和微任务。

**宏任务**通常包括定时器、网络 IO、用户交互、渲染相关的。如 setTimeout, setInterval, setImmediate, I/O, UI rendering，dispatch。

**微任务**通常是 Promise, process.nextTick, Object.observe, MutationObserver, MutaionObserver。

## 浏览器 eventloop

浏览器的 Event Loop 模型在[HTML5 标准](https://www.w3.org/TR/html5/webappapis.html#event-loops)中有明确定义，但每个浏览器有稍微不同的具体实现。

### 运行流程

* EV 的触发时机是主线程 call stack 为空。
* 清空 microtask 队列，直到没有新的 microtask 加入。
* 取出最早的一个 task，执行。
* 在执行 task 过程中，如果 call stack 为空，也立即清空 microtask。

### 定时器行为

HTML5 标准规定的 setTimeout 最短间隔不得低于 4 毫秒。但浏览器有可能有不同实现。

setTimeout 第二个参数传入的时间，只是「可以执行」的时间，不是「真正执行」的时间。如果 call stack 一直不为空，那就一直没有机会执行。

## Node eventloop

### libuv

#### Libuv 基本机制

[https://zhuanlan.zhihu.com/p/50497450](https://zhuanlan.zhihu.com/p/50497450)

操作系统能力（pollable）：sockets \(net/dgram/http/tls/https/child\_process pipes, stdin, out, err\), timeouts and intervals.

自建线程池： file system operations, dns.lookup\(\) and some crypto functions.

核心代码在 deps/uv/src/unix/core.c （[代码解析文章](https://cnodejs.org/topic/5a9108d78d6e16e56bb80882)）

#### 核心概念

handle 代表了持久性对象。在异步的操作中，相应的 handle 上有许多与之关联的 request。

简单理解，handle （句柄）就是一个对象，用来保存持久性对象的状态。常见的句柄，例如 tcp 句柄、udp 句柄、进程句柄、文件流句柄。

request 是短暂性对象（通常只维持在一个回调函数的时间），通常对映着 handle 上的一个 I/O 操作。request 用来在初始函数和回调函数之间，传递上下文。

#### 六个阶段

**timers**

执行所有超时的计时器回调。

```text
执行机制并不是在任务到期之后，把回调放入所谓的「timer queue」，而是用最小堆管理所有的计时器任务。根据最小堆的性质，每次判断头部节点有没有超时，没有的话说明后面的节点也不会超时，有的话继续往下判断即可。
```

**IO callbacks**

相当于运行 io 的 callback，也就是 stream 流式传输的回调，比如我用 libuv 中提供的流读文件，或者 IPC 之类的。

**idle**

存放用户自定义的回调，并在每次事件循环时触发一次。

```text
idle 和 prepare 最大的区别在于，当存在活跃的 idle 时，poll 的超时时间会被设置为 0。
idle 是为了越过 poll 直接执行 check 的，所以 idle 必须在前面。
由于 idle 每次事件循环都会执行一次回调，因此在实现上，回调出队执行完成后，会重新回到对尾。其他阶段的回调都是用完即丢。
setImmediate 是利用 idle 这个阶段实现的。
```

**prepare**

poll 之前的缓冲，因为 poll 会阻塞，idle 又可以越过 poll，所以可以单独在 prepare 阶段特殊处理，嵌入特定 idle。

**poll**

阻塞等待执行回调。带阻塞超时。

```text
在 poll 开始之前，会计算超时时间。
超时时间为距离现在最近的 timer。

在满足以下条件时，超时时间会被设置为 0：
- 显式设置为非阻塞模式：uv_run 处于 UV_RUN_NOWAIT 模式下
- 显式调用 stop：uv_stop()被调用
- 没有活跃的 handles 和 request
- 有活跃的 idle handles
- 有等待关闭的 handles

如果都不满足，则会一直阻塞。

poll 开始后，会发生以下几种常见：
- callback queue 中的回调依次被执行，直到超时时间到，poll 结束。
- 如果没有超时，且 queue 队列为空，则阻塞，直到下一个 fd 返回。
- 如果阻塞过程中，有新的 timer 到期，则跳回 timers 阶段。

阶段为什么放到后边，因为有 uv_stop 的存在，你可以用 uv_stop 来关闭事件循环，而这个开关是放在 prepare 和 poll 之间的，也就是如果我在 poll 之后 stop 了，libuv 还会跑一次循环直到跑完 prepare，这样正好保证了 IO callbacks 的完整退出。
```

**check**

**close**

循环关闭所有的 closing handles。

### Node 实现

#### 基本流程

[https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)

* [https://zhuanlan.zhihu.com/p/35039878](https://zhuanlan.zhihu.com/p/35039878)
* nodejs 在 libuv 的六阶段里，分别作了这些事情：
* timers：执行 setTimeout 和 setInterval 中到期的回调。
* I/O callbacks：执行上一轮残留的一些回调。
* idle：仅内部使用，用来控制 setImmediate 的执行。
* prepare：仅内部使用。
* poll：除了其他环节的少量回调，其他所有任务都在这里完成。
* check：执行 setImmediate 的回调。
* close callbacks：循环关闭所有的 closing handles，如 socket.on\("close", cb\)。

每个阶段执行完成后，都会检查并执行微任务。同一个微任务队列中 process.nextTick 优先于 promise 执行。

#### setImmediate

setImmediate 和 setTimeout 谁先谁后的问题。这个其实是不一定的。从 uv\_run 中我们看到执行定时器的代码比是比 uv\_\_run\_check 先的。这种情况下，执行先后是随机的。

```text
setTimeout(() => {
  console.log('timeout');
}, 0);

setImmediate(() => {
  console.log('immediate');
});
```

但是如果我们在执行完定时器之后，uv\_\_run\_check 之前，又新增了一个定时器和执行了 setImmediate，那么 setImmediate 的回调就会先执行。即，如果把这两个放在同一个异步回调中，那 setImmediate 就一定会比 setTimeout 早执行。

```text
const fs = require('fs');

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('timeout');
  }, 0);
  setImmediate(() => {
    console.log('immediate');
  });
});
```

* nodejs 是通过 idle 阶段设置一个活跃的 idle handle 开关，使得 poll 超时时间为 0，从而跳过 poll 阻塞，进入 check 阶段，并在 check 阶段执行 setImmedate 回调。

#### Process.nexttick

在调用栈为空，且在evenloop 前执行。每个 eventloop 的阶段开始前执行。

应用场景

```text
const EventEmitter = require('events');
const util = require('util');

function MyEmitter() {
  EventEmitter.call(this);

  // use nextTick to emit the event once a handler is assigned
  process.nextTick(() => {
    this.emit('event');
  });
}
util.inherits(MyEmitter, EventEmitter);

const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
  console.log('an event occurred!');
});
```

