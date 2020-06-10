# HTTPS

## HTTPS

* [https://www.jianshu.com/p/b0b6b88fe9fe](https://www.jianshu.com/p/b0b6b88fe9fe)

## Why HTTPS

HTTP 的不安全？

内容可能被窃听、篡改，对端可能被伪装。

反观HTTPS协议，它比HTTP协议相比多了以下优势（下文会详细介绍）:

数据隐私性：内容经过对称加密，每个连接生成一个唯一的加密密钥 数据完整性：内容传输经过完整性校验 身份认证：第三方无法伪造服务端（客户端）身份

## 如何得到证书

* 生成 CSR（证书申请文件）、私钥
* 提交 CSR 文件到 CA，CA 进行签名
* 拿到内容，hash，用自己的私钥进行签名

## SSL 握手

### 验证阶段

* client hello：发送一个支持的协议、加密、压缩方法、以及**随机数1**。
* server hello：确认使用的协议、加密、压缩，发送**随机数2**，**服务器证书**。
* 客户端验证 用系统内置的 CA 公钥解密证书，得到摘要 客户端自己 hash，得到摘要，比对两次摘要是否一致

### 秘钥交换阶段

如果使用 RSA，则需要传递 PMS。

* 客户端生成 Pre-Master Secret，用证书公钥加密，发送给服务器
* 客户端和服务器各自通过 PMS、随机数1、随机数2，计算得到master key。

如果使用 Diffie — Hellman 算法，则无需出传递 PMS。

* 让双方在完全没有对方任何预先信息的条件下通过public channel （unsafe channel） 建立起一个密钥。

### 传递阶段

使用 master key 对称加密，这样就可以将加密后的内容在不安全的 HTTP 中进行传输。

## 加密算法原理

RSA、DH

[https://medium.com/@clu1022/%E9%82%A3%E4%BA%9B%E9%97%9C%E6%96%BCssl-tls%E7%9A%84%E4%BA%8C%E4%B8%89%E4%BA%8B-%E4%B9%9D-ssl-communication-31a2a8a888a6](https://medium.com/@clu1022/%E9%82%A3%E4%BA%9B%E9%97%9C%E6%96%BCssl-tls%E7%9A%84%E4%BA%8C%E4%B8%89%E4%BA%8B-%E4%B9%9D-ssl-communication-31a2a8a888a6)

## 中间人攻击

在这种通信过程中，客户端或者操作系统内置了权威CA\(certification authority\)的根证书，而服务器在通信之初，会先返回在CA那里获取的签名证书，然后客户端用根证书验证证书有效性，最后使用验证通过的证书提供的公钥加密数据。

简单提一下，目前最有效的加密方式可能是量子加密\(Quantum cryptography\)。量子加密同时拥有数学和量子力学两大学科加持，只要数学和量子理论没有问题，那么量子加密理论上就是绝对安全的。关于量子加密，以后会专门讲一下。

