# HTTP

## HTTP

### HTTP1 和 1.1 区别

* 缓存：增加 Etag
* 带宽优化及网络连接：增加断点续传（range）
* 错误通知：新增状态码（409、410）
* Host 头处理
* 长连接：默认开启 Connection： keep-alive

### HTTP 请求头

* 来源相关
* origin
* referer
* host
* 缓存相关
* If-Unmodified-Since, If-Match, If-None-Match
* Accept

### HTTP 响应头

* 缓存相关
* cache-control
* etag
* last-modified

## 状态码

### 1xx

### 2xx：成功

* 200
* 204：预检通过
* 206：range

### 3xx：重定向

* 301：永久移动
* 302：临时移动
* 304：未修改（缓存相关）
* 307：临时移动（区别 \#tbd）

### 4xx：请求错误

* 401：未授权
* 409（Conflict）表示请求的资源与资源的当前状态发生冲突
* 410（Gone）表示服务器上的某个资源被永久性的删除。

### 5xx：服务器错误

* 500：内部错误
* 504：网关超时

## METHOD

### GET

### POST

**application/x-www-form-urlencoded**

title=test&sub%5B%5D=1&sub%5B%5D=2&sub%5B%5D=3

**application/json**

{"a":"a"}

**multipart/form-data**

```text
------WebKitFormBoundarybUB4deZjYLxS31hM
Content-Disposition: form-data; name="filepath"; filename="中二病也要谈恋爱 136c06c5e486d2531439acb9e362f307c93d74a9.torrent"
Content-Type: application/octet-stream
------WebKitFormBoundarybUB4deZjYLxS31hM--
```

### GET\POST 的区别

|  | GET | POST |
| :--- | :--- | :--- |
| 后退/刷新 | 不会重新提交 | post 会被重新提交 |
| 缓存 | get 可以被缓存 | 不可以 |
| 书签 | 可被收藏 | 不可以 |
| 数据长度 | url 长度最长为 2048 个字符 | 无限制 |
| 安全 | get 数据对所有人可见 | 相对安全 |

## 报文

### formData/boundary

……？

### 传输

* 一次传输

### range

Transfer-Encoding: chunked

请求头 Range:bytes=0- Range: range-unit=first-byte-pos "-" \[last-byte-pos\]

响应头 Accept-Ranges: bytes Content-Range: bytes 0-499/1234

206

* 传输之后如何关闭

## keep-alive

### 为什么要使用长连接？

在一些 TPS/QPS 很高的 REST 服务中，如果使用的是短连接（即没有开启keep-alive），则很可能发生客户端端口被占满的情形。这是由于短时间内会创建大量TCP 连接，而在 TCP 四次挥手结束后，客户端的端口会处于 TIME\_WAIT一段时间\(2\*MSL\)，这期间端口不会被释放，从而导致端口被占满。这种情况下最好使用长连接。

### 重定向机制

