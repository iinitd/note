# PWA

PWA

PWA 不是特指某一项技术，而是应用了多项技术的 Web App。其核心技术包括 App Manifest、Service Worker、Web Push，等等。

## App Manifest

manifest.json 是一个简单的JSON文件。

里面包括 icons、name、start\_url等，实现「添加到桌面」的功能。

## Service Worker

Service worker 独立线程。

实现资源缓存。

### 注册

```text
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js')
  .then(function(registration) {
    console.log('Registration successful, scope is:', registration.scope);
  })
  .catch(function(error) {
    console.log('Service worker registration failed, error:', error);
  });
}
```

### 安装

```text
var cacheName = 'cachev1'
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open(cacheName).then(function(cache) {
      return cache.addAll(
        [
          '/css/bootstrap.css',
          '/css/main.css',
          '/js/bootstrap.min.js',
          '/js/jquery.min.js',
          '/offline.html'
        ]
      );
    })
  );
});
```

### 激活

```text
self.addEventListener('activate', function (event) {
    event.waitUntil(
        Promise.all([
            // 更新客户端
            self.clients.claim(),

            // 清理旧版本
            caches.keys().then(function (cacheList) {
                return Promise.all(
                    cacheList.map(function (cacheName) {
                        if (cacheName !== 'cachev1') {
                            return caches.delete(cacheName);
                        }
                    })
                );
            })
        ])
    );
});
```

## Web Push

### 获取授权

```text
// main.js
Notification.requestPermission(function(status) {
    console.log('Notification permission status:', status);
    //status 会有三个取值default granted denied 分别代表： 默认值（每次访问页面都询问）、 允许、拒绝
});
```

### 发送通知

```text
// main.js
function displayNotification() {
  if (Notification.permission == 'granted') {
    navigator.serviceWorker.getRegistration().then(function(reg) {
      reg.showNotification('Hello world!');
    });
  }
}
```

### 响应事件

```text
// service-worker.js
self.addEventListener('notificationclick', function(e) {
  var notification = e.notification;
  var primaryKey = notification.data.primaryKey;
  var action = e.action;

  if (action === 'close') {
    notification.close();
  } else {
    clients.openWindow('http://www.example.com');
    notification.close();
  }
});
```

