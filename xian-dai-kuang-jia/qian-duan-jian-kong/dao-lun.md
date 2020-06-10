# 导论

问了监控系统，如何计算白屏时间和首屏渲染时间的，如何进行数据上报的，上报到监控系统展示是怎样的一个过程

## 时间指标

### 白屏时间

```text
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>白屏时间</title>
    <script>
        // 开始时间
        window.pageStartTime = Date.now();
    </script>
    <link rel="stylesheet" href="">
    <link rel="stylesheet" href="">
    <script>
        // 白屏结束时间
        window.firstPaint = Date.now()
    </script>
</head>
<body>
    <div>123</div>
</body>
</html>

白屏时间 = firstPaint - pageStartTime
```

### 首屏时间

```text
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首屏时间</title>
    <script>
        // 开始时间
        window.pageStartTime = Date.now();
    </script>
    <link rel="stylesheet" href="">
    <link rel="stylesheet" href="">
</head>
<body>
    <div>123</div>
    <div>456</div>
    // 首屏可见内容
    <script>
        // 首屏结束时间
        window.firstPaint = Date.now();
    </script>
    // 首屏不可见内容
    <div class=" "></div>
</body>
</html>

首屏时间 = firstPaint - pageStartTime
```

### 统计首屏内加载最慢的图片/iframe（更常用）

```text
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>首屏时间</title>
    <script>
        window.pageStartTime = Date.now()
    </script>
</head>
<body>
    <img src="https://lz5z.com/assets/img/google_atf.png" alt="img" onload="load()">
    <img src="https://lz5z.com/assets/img/css3_gpu_speedup.png" alt="img" onload="load()">
    <script>
        function load () {
            window.firstScreen = Date.now()
        }
        window.onload = function () {
            // 首屏时间
            console.log(window.firstScreen - window.pageStartTime)
        }
    </script>
</body>
</html>
```

### 可操作时间

```text
// 原生JS实现dom ready
window.addEventListener('DOMContentLoaded', (event) => {
    console.log('DOM fully loaded and parsed');
});
```

### 总下载时间

总下载时间即window.onload触发的时间节点。

### 性能相关

DNS查询耗时 = domainLookupEnd - domainLookupStart TCP链接耗时 = connectEnd - connectStart request请求耗时 = responseEnd - responseStart 解析dom树耗时 = domComplete - domInteractive 白屏时间 = domloading - fetchStart domready可操作时间 = domContentLoadedEventEnd - fetchStart onload总下载时间 = loadEventEnd - fetchStart

