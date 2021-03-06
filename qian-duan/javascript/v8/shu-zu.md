# 数组

[https://zhuanlan.zhihu.com/p/26169639](https://zhuanlan.zhihu.com/p/26169639)

V8里面所有的数据类型的根父类都是Object，Object派生HeapObject，提供存储基本功能，往下的JSReceiver用于原型查找，再往下的JSObject就是JS里面的Object，Array/Function/Date等继承于JSObject。左边的FixedArray是实际存储数据的地方。

![](https://github.com/iinitd/note/tree/b95761b12cf280e50b224a1cf085bb77983a7e61/V8/%7B$WP_ASSETS%7D/9994d772f743e4a2d094a7f871b9a1d9.png)

## 基本概念

JSArray 是继承自JSObject，也就是说，数组是一个特殊的对象。

数组分为快数组和慢数组。

快数组的底层是 FixedArray ，并且数组长度 &lt;= elements.length\(\);

慢数组的底层是一个以数字为键的 HashTable。

### 数组的创建

新建的空数组默认是快数组。

数组的变长扩容实现机制是 `扩容后的新容量 = 旧容量的1.5倍 + 16`。扩容后会将数组拷贝到新的内存空间中。

对于分配了空间，还没存放元素的位置，存放的是 holes 对象，访问的时候得到 undefined。

### 数组的内部转换

快数组转为慢数组。

新增的 index - capacity &gt;=1024，即产生 1024 以上的 holes 时。

```text
let a = [1, 2]
a[1030] = 1;
```

慢数组转快数组，检查到 holes 少于一定数量。（holes 少于一半）

```text
let a = [1,2];
a[1030] = 1;
for (let i = 200; i < 1030; i++) {
    a[i] = i;
}
```

快数组就是以空间换时间的方式，申请了大块连续内存，提高效率。 慢数组以时间换空间，不必申请连续的空间，节省了内存，但需要付出效率变差的代价。

### ArrayBuffer

从底层申请一块连续区域，需要通过视图访问。

```text
var bf = new ArrayBuffer(1024); 
var b = new Int32Array(bf);
```

排序：[https://github.com/mqyqingfeng/Blog/issues/52](https://github.com/mqyqingfeng/Blog/issues/52)

[https://juejin.im/post/5d80919b51882538036fc87d](https://juejin.im/post/5d80919b51882538036fc87d)

