# 多线程

## 基本概念

主进程里面有多个线程：

* js 主线程
* 事件循环
* libuv 工作池线程（fs、crypto）

worker\_threads 模块

Node.js 通过提供 cluster、child\_process API 创建子进程的方式来赋予Node.js “多线程”能力。但是这种创建进程的方式会牺牲共享内存，并且数据通信必须通过json进行传输。（有一定的局限性和性能问题）

## 多进程

每个 worker 进程通过使用 child\_process.fork\(\) 函数，基于 IPC（Inter-Process Communication，进程间通信），实现与 master 进程间通信。

多进程运行还涉及父子进程通信，子进程管理，以及负载均衡等问题，这些特性 cluster 帮你实现了。

```text
// 素数的计算
const min = 2
const max = 1e7 // = 10000000
let primes = []


if (cluster.isMaster) {
    const range = Math.ceil((max - min) / numCPUs)
    let start = min

    for (var i = 0; i < numCPUs; i++) {
        const worker = cluster.fork() // 启动子进程
        //  在主进程中，这会发送消息给特定的工作进程
        worker.send({ start: start, range: range })

        start += range

        worker.on('message', (msg) => {
            primes = primes.concat(msg.data)
            worker.kill()
        })

    }
    // 当任何一个工作进程关闭的时候，cluster 模块都将会触发 'exit' 事件
    cluster.on('exit', function(worker, code, signal) {
        console.log('worker ' + worker.process.pid + ' died')
    })
} else {
    // 监听子进程发送的信息
    process.on('message', (msg) => {
        console.log(msg)
        const { start, range} = msg
        const data = generatePrimes(start, range)
        // 在工作进程中，这会发送消息给主进程
        process.send({ data: data })
    })
}
```

## 多线程

worker\_threads 比使用 child\_process 或 cluster可以获得的并行性更轻量级。 此外，worker\_threads 可以有效地共享内存。

```text
const { Worker, isMainThread, parentPort } = require('worker_threads');
if (isMainThread) {
  // This code is executed in the main thread and not in the worker.

  // Create the worker.
  const worker = new Worker(__filename);
  // Listen for messages from the worker and print them.
  worker.on('message', (msg) => { console.log(msg); });
} else {
  // This code is executed in the worker and not in the main thread.

  // Send a message to the main thread.
  parentPort.postMessage('Hello world!');
}
```

## 多进程的其他实现

spawn\(\)，exec\(\)，execFile\(\) 和 fork\(\)

spawn 基于 stream，可以通过 stdio 通信。

