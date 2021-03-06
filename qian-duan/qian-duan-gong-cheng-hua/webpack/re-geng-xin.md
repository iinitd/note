# 基本思想

基于 webpack-dev-server、webpack-dev-middleware、HotModuleReplacementPlugin 实现的。

修改 entry，加入

webpack-dev-server/client/index.js：浏览器 WS 建立

webpack/hot/dev-server.js：热更新逻辑

注册 compiler.watch 回调，自动完成后将编译后的文件写入内存（memory-fs）

注册 webpack 的 done hook 回调。每次 webpack 编译完成，就向浏览器的 ws client 发送 hash 和 ok 消息。

ws client 接收到 hash 和 ok，触发 webpackHotUpdate 事件。

触发事件后，调用 HotModuleReplacementPlugin 提供的 moudle.hot.check 方法，检查更新。

使用 jsonp 获取到更新部分，apply 方法负责更新和替换，hotAddUpdateChunk。

最后调用 **webpack\_require**\(moduleId\) 执行最新的代码。

![](https://github.com/iinitd/note/tree/b95761b12cf280e50b224a1cf085bb77983a7e61/现代框架/Webpack/%7B$WP_ASSETS%7D/60734a1ef5d3895fafcb69bd12c356f6.png)

[https://juejin.im/post/5de0cfe46fb9a071665d3df0](https://juejin.im/post/5de0cfe46fb9a071665d3df0)

[https://www.html.cn/doc/webpack2/concepts/hot-module-replacement/](https://www.html.cn/doc/webpack2/concepts/hot-module-replacement/)

服务器向浏览器推送信息，除了 WebSocket，还有一种方法：Server-Sent Events（以下简称 SSE）。本文介绍它的用法。

基于 HTTP。除了IE/edge，其他都支持。

服务器发送单向数据流的通信方式。

SSE client

```text
var source = new EventSource(url, { withCredentials: true });

source.addEventListener('open', function (event) {
  // ...
}, false);

source.addEventListener('message', function (event) {
  var data = event.data;
  // handle message
}, false);
```

SSE server

```text
var http = require("http");

http.createServer(function (req, res) {
  var fileName = "." + req.url;

  if (fileName === "./stream") {
    res.writeHead(200, {
      "Content-Type":"text/event-stream",
      "Cache-Control":"no-cache",
      "Connection":"keep-alive",
      "Access-Control-Allow-Origin": '*',
    });
    res.write("retry: 10000\n");
    res.write("event: connecttime\n");
    res.write("data: " + (new Date()) + "\n\n");
    res.write("data: " + (new Date()) + "\n\n");

    interval = setInterval(function () {
      res.write("data: " + (new Date()) + "\n\n");
    }, 1000);

    req.connection.addListener("close", function () {
      clearInterval(interval);
    }, false);
  }
}).listen(8844, "127.0.0.1");
```

