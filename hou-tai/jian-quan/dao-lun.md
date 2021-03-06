# 导论

## 鉴权方案

### 鉴权方案

* token
* session
* cookie 跟 session 通过 sessionId 建立联系，当浏览器访问服务器时服务器会为该会话生成一个 session，并将 sessionId 通过 set-cookie 响应头返回给客户端，客户端解析到 set-cookie 响应头会将 sessionId 存储到 cookie 中，之后的每次会话都会将 cookie 作为请求头带给服务端，服务端根据 cookie 中的 sessionId 区分此次会话
* jwt
* 如何进行 js 异常注入，如何解决 js 异常注入
* 劫持 cookie 的可能场景
* DOS 攻击如何解决、DNS 欺骗如何解决、ARP 欺骗、SQL 注入、XSS、CSRF、iframe 安全问题、本地存储数据问题、第三方依赖的安全性问题。
* 点击劫持 指第三方网站通过 iframe 内嵌某一个网站，并且将 iframe 设置为透明不可见，将其覆盖在其他经过伪装的 DOM 上，伪装的可点击 DOM（按钮等）与实际内嵌网站的可点击 DOM 位置相同，当用户点击伪装的 DOM 时，实际上点击的是 iframe 中内嵌的网页的 DOM 从而触发请求操作。点击劫持的最大特点是用户自己触发了点击事件，但是毫不知情。
* 鉴权方案
* session
* jwt
* Access token 放 header 防止 CSRF, Refresh token 应该是用 httpOnly 的 cookie 来防止 XSS. Jwt 自带鉴权，可以在分布式，微服务里面独立 verify，无需一个 centralized auth 服务。而 refresh token 用来做用户管理，revoke, 禁用用户，权限管理配置等等
* token

## OAuth2

1. 客户端向用户请求授权，得到 授权码。
2. 客户端打开认证服务器，用户确认授权后，认证服务器访问客户端的重定向 URI，并带上授权码。
3. 用户在客户端内提供用户名密码，客户端拿用户名密码找认证服务器请求授权。
4. 客户端拿到 授权码 去认证服务器申请 access token/refresh token
5. 客户端拿到 access token，去资源服务器请求资源。
6. access token 过期，则拿 refresh token 申请新的 access token。

## session

1、用户向服务器发送用户名和密码。

2、服务器验证通过后，在当前对话（session）里面保存相关数据，比如用户角色、登录时间等等。

3、服务器向用户返回一个 session\_id，写入用户的 Cookie。

4、用户随后的每一次请求，都会通过 Cookie，将 session\_id 传回服务器。

5、服务器收到 session\_id，找到前期保存的数据，由此得知用户的身份。

## JWT

基本格式：Header.Payload.Signature

算法：

Signature 是对 header 和 payload 的加密，只有服务器端才知道。

```text
HMACSHA256(
base64UrlEncode(header) + "." +
base64UrlEncode(payload),
secret)
```

使用：

`Authorization: Bearer <token>`

缺点

1. 明码传输，因此尽量使用 HTTPS
2. 因为服务器端无存储，无法提前废除
3. JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。

优点： 1. 服务器端无存储。 2. JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。

## 权限系统

rbac

RBAC0：用户-角色-权限

RBAC1：子角色

RBAC2：角色互斥

一张是 权限 表，一张是 用户-权限 对应表，还有一张是 路由-权限 对应表，这样基本能覆盖 RBAC 2 以上的需求了。

