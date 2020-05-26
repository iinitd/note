
# Proxy

* 基本概念

* Proxy 是一个架设在目标对象之上的拦截器对象。

* 构造函数传入两个参数，第一个是目标对象，第二个是配置对象。

* 配置对象可以配置十三种拦截操作，常用的有 get / set / construct / apply / deleteProperty 等。

* Object.defineProperty 的对比

* defineproperty 无法一次性监听所有属性

* 无法监听新增属性

* 无法监听数组操作（vue中是通过hack了几个常用的原型方法实现监听的）

* 无法监听除了get / set 之外的对象操作。

* 唯一的优点：兼容性比 proxy 好一点。

* 应用

* get：实现禁止访问私有属性（如下划线开头）

* get：实现未定义函数默认值

* set：实现格式校验

* apply：拦截调用，实现调用次数、耗时统计 #diy

* get：safe-touch #p1，实现 optional 可选链 #diy

* get：pipe（https://mp.weixin.qq.com/s/Z3_AfTy84h-ojhljnQJqIg）

# 入参

读取属性时触发该方法，参数如下：target —— 是目标对象，该对象作为第一个参数传递给 new Proxy，property —— 目标属性名,receiver —— 如果目标属性是一个 getter 访问器属性，则 receiver 就是本次读取属性所在的 this 对象。通常，这就是 proxy 对象本身（或者，如果我们从代理继承，则是从该代理继承的对象）。现在我们不需要此参数，因此稍后将对其进行详细说明。

---

https://juejin.im/post/5e6cdf25e51d4526f16e6294