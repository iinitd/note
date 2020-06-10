# 全流程

当我们打开浏览器

render 和 v8 什么关系 [https://juejin.im/post/5d86496be51d453bdb1d9c11](https://juejin.im/post/5d86496be51d453bdb1d9c11)

dom binding：[https://blog.csdn.net/scusyq/article/details/7648489](https://blog.csdn.net/scusyq/article/details/7648489)

## 打开浏览器

### 创建多个进程

创建：主进程、渲染进程、插件进程、网络进程、GPU 进程

在资源充足情况下，主进程还会进一步拆分出文件进程、网络进程、设备进程、音视频进程等。

进程间通过 IPC 通信。

## 浏览器导航阶段

浏览器导航阶段从「输入网址」到「准备渲染」。

### 输入网址

### 缓存查询

走缓存机制。

### DNS 解析

网络层还会发生 ARP，从 ip 到 MAC 地址。

解析过程：本地缓存、根域名服务器、一级域名服务器、……。

UDP 无需建立连接。

### 建立连接

根据协议建立连接，HTTP、HTTPS、WS……。

HTTP 协议：TCP 三次握手。

HTTPS 协议：TCP 三次握手、SSL 握手。

WebSocket：同 HTTPS……？

chrome 规定同一域名同时最多建立 6 个连接，多的连接进入队列，不满 6 个则直接建立连接。

### 发送请求

HTTP 明文传输。请求行、请求头、请求体。

HTTPS 对称加密传输。

TCP 拥塞控制、超时重传。

### 接收响应

常见的响应码。响应头。

重定向，拿到 location 字段，再次发送请求。

### 连接复用

[https://www.jianshu.com/p/67d5d46b4e84](https://www.jianshu.com/p/67d5d46b4e84)

在 HTTP1.1 中，多个连接是不能重叠的。对于多个连接，浏览器会优先协商是否可以使用 HTTP2，否则建立最大连接数（6 个）以提高速度。

在 HTTP2 中，实现了多路复用。实现机制……？

### 关闭连接

请求头声明了 keep-alive，则等待 keep-alive time。如果期间有请求，则重置等待时间。

到期了或声明 Connection: close，则由服务器端关闭。

### 拿到响应

浏览器根据 MIME 类型决定如何使用响应。

CORB 检测……？

html 页面渲染。

### 准备渲染

当浏览器拿到响应头中的 content-type 为 html，则开始该阶段。不是等到响应体全部接收完才开始的。

网络进程通知主进程「准备渲染」

创建渲染进程

chrome：process-per-site-instance，same-site 会运行在同一个渲染进程中。same-site 是根域名+协议，与端口、子域名、路径无关。

主进程通知渲染进程「提交文档」

渲染进程和网络进程进行 IPC 通信

传输完成后，渲染进程通知主进程「确认提交」

主进程更新界面状态（地址栏、历史记录等）

因此，在此之前，浏览器还是停留在上一个页面。

## 浏览器渲染阶段

页面的渲染进程是在沙箱模式中完成的。

### 构建 DOM 树

DOM 是什么？DOM 是表达页面的数据结构，是 JavaScript 操作页面的接口，是从文档到页面的中间层，可以用于拦截不安全内容。

主线程接收源源不断的新数据，调用 HTML 解析器解析 HTML，生成 DOM 树。

### 解析过程

分词器：字节流-标记流

解析器：将 token 解析为 DOM，挂载到 DOM 树（开关标签匹配的栈）

**XSSAuditor** 检查语法安全：是否引用了外部脚本，是否符合 CSP 规范，是否存在跨域请求等。

js 和 css 的阻塞

JavaScript 会阻塞 DOM 生成，而样式文件又会阻塞 JavaScript 的执行

解析到 script 标签时，停止解析，阻塞 DOM 构建。

* script 内嵌的代码：调用 JS 引擎执行内容，JS 引擎修改 DOM。
* scirpt 引用外部文件：交给网络线程去下载，然后执行。
* 浏览器优化可能将下载提前，接收到字节流之后开启预解析线程。
* 如果没有 DOM 相关操作，则可以使用 aysnc 或 defer。
* js dom binding……？
* ……event loop？
* js 执行前无法预知是否操作了 CSSOM，因此，js 执行依赖样式表加载完成。

  x

解析白屏：

从确认提交到首次渲染中间的白屏。

主要瓶颈和优化策略

```text
- 优化网络，使用缓存机制和 CDN

```适当使用内联
减少资源个数，压缩文件大小
使用 async 或 defer（defer：渲染完执行，async：下载完执行）
使用媒体查询拆分 css
使用 web worker 减少非 DOM 操作的阻塞在主线程的时间
使用 CSS 动画代替 JS 动画

webkit 的一个阶段叫做 XSSAuditor 干啥的，发生在哪个阶段（我贼熟悉 webkit，但恰恰这个忘了，作用猜出来了是过滤 xss 的，阶段猜错了）

从 webkit 底层原理分析前端优化

理解 WebKit 和 Chromium：https://www.ituring.com.cn/book/miniarticle/40626

## 样式计算

解析 CSS 文本，转换为 document.styleSheets 结构。

属性标准化，将 em 转 px，将颜色转为标准 rgba 值，等。

根据继承和层叠关系，生成每个 DOM 的 ComputedStyle 结构。

styleSheets 是 CSSOM 的一个接口。

## 布局阶段

遍历 DOM 树，过滤不可见元素，生成布局树。

遍历布局树，计算每个节点的位置，写入节点，在过程中会反复更新。

## 分层阶段

浏览器的页面是分层的。

分层的条件（任一）：

    - 需要剪裁
    - 拥有层叠上下文（明确定位属性 position 或 z-index、透明度、CSS 滤镜、等）


构建图层树 Layer tree

图层绘制，输出绘制指令列表

    - 在 devtool 的 layer 标签中可以查看绘制过程
    - 绘制的 API 如：drawRect、drawImageRect、drawTextBlob 等。

## 栅格化、合成、GPU 加速

合成是在单独的线程中完成的。渲染进程中的主线程完成到构建到分层的步骤之后，将绘制列表提交给合成线程。

将图层划分成图块（tile）

由栅格化线程池进行栅格化，即将图块转换为位图。优先生成视口（viewport）附近的位图。
栅格化任务通过 IPC 交给 GPU 进程来完成。

栅格化完成后，合成线程生成 DrawQuad 命令，提交给浏览器进程。

浏览器进程中的 viz 组件接收命令，将内容绘制到显卡缓冲区，最终显示到屏幕。

通过栅格化线程池计算。

以上过程可以用来解释一些常见的问题？

重排：需要渲染进程重新走一遍，开销最大。

重绘：可以跳过布局和分层。

CSS 动画为什么性能高？
无需重新执行主线程中的任务，直接在合成线程中完成，因此性能高。

性能优化实践
```

* 将触发重排重绘的操作放在一起，使其在一次重新渲染中完成。

\`\`\`通过虚拟 diff 计算总差异，统一修改。 使用 will-change 告诉浏览器为其准备独立的层

## 浏览器更新阶段

浏览器的页面是逐帧绘制的，为了达到不卡顿，FPS 为 60Hz。也就是一帧 16ms。

浏览器在一帧内要完成的工作包括

处理用户的交互 JS 解析执行 帧开始。窗口尺寸变更，页面滚去等的处理 rAF\(requestAnimationFrame\) 布局 绘制

因此，对于耗时较长的 js 执行，要着重进行优化。（相关知识点：React Fiber）

## 全流程优化

[http://blog.404mzk.com/cong-shu-ru-url-dao-zhao-xing-neng-you-hua-dian.html](http://blog.404mzk.com/cong-shu-ru-url-dao-zhao-xing-neng-you-hua-dian.html)

DNS，域名解析用 CNAME，配置到 CDN，加快资源加载。

