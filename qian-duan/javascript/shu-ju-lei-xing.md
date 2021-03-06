# 数据类型

## 数据类型

### 基本类型（Primitive）

Undefined、Null、Boolean、Number、String、Symbol、bigint \#tbd

浮点数 0.1+0.2=0.3？

* 非是 ECMAScript 独有
* IEEE754 标准中 64 位的储存格式，比如 11 位存偏移值
* 其中涉及的三次精度丢失

### 引用类型（Reference）

* 数组：[https://juejin.im/post/5e6ebfa86fb9a07ca714d0ec](https://juejin.im/post/5e6ebfa86fb9a07ca714d0ec)

## 类型判断

### typeof

判断基本类型和function时用。

输出结果为 undefined、boolean、string、number、object、function、symbol 之一

原理：底层变量存储的时候在头3位会存类型信息，000对象，0null，1整数，010浮点，100字符串，110布尔。

因此 type null == 'object'

用处 检测一个元素是否为undefined，或者是否为function。

### instanceof/isPrototypeOf

用途：判断对象是不是某个构造函数的实例 原理：沿着原型链找

### Object.prototype.toString

原理：降级输出 \[object type\]

\[\[NativeBrand\]\] \[\[class\]\]

Boolean, Number, String, Function, Array, Date, RegExp, Object, Error等等。

```text
(obj === null || obj === undefined) ? String(obj) : Object.prototype.toString.call(obj).match(/\[object (\w+)\]/)[1].toLowerCase()
```

判断是否为对象

Object.prototype.toString.call\(obj\) === '\[Object Object\]'

### 判断数组

* 1.Array.isArray\(arr\) 
* 2.Object.prototype.toString.call\(arr\) === '\[Object Array\]'
* 3.arr instanceof Array
* 4.array.constructor === Array

### 判断是否为基本类型

Object\(obj\) === obj

[https://stackoverflow.com/questions/24460874/what-does-objectobj-obj-do](https://stackoverflow.com/questions/24460874/what-does-objectobj-obj-do)

* console.log\(Object\('foo'\) === 'foo'\); // false
* console.log\(Object\(true\) === true\);  // false
* console.log\(Object\(null\) === null\);  // false
* var obj = {};console.log\(Object\(obj\) === obj\);   // true

## 类型转换

### 显示类型转换

* parseInt\(‘42 pas’\) Number\(‘42 pas’\) 分别等于什么

### 隐式类型转换

[https://juejin.im/post/5a7172d9f265da3e3245cbca](https://juejin.im/post/5a7172d9f265da3e3245cbca)

### == 判断true/false 常见题目

* 为什么null == undefined？区别是什么？Number\(null\)和Number\(undefined）？

### ‘A’ ‘B’ + ‘2’ 等于什么

### 拆箱和装箱

* [https://juejin.im/post/5cf62c7ae51d454fd8057b3d](https://juejin.im/post/5cf62c7ae51d454fd8057b3d)

## 对象拷贝

### 浅拷贝

* `Object.assign({}, obj1)`
* `arr.concat()`

### 深拷贝

注意点：

* 递归爆栈
* 循环依赖
* 多种数据类型的处理：对象、数组、map/set、基本类型、函数、symbol、Date、RegExp 等。

```javascript
function deepClone(obj, hash = new WeakMap()) {

    if (obj == null) return obj;
    if (Object(obj) !== obj) return obj;
    if (obj instanceof Function) return obj;
    if (obj instanceof RegExp) return new RegExp(obj); 
    if (obj instanceof Date) return new Date(obj);

    if (hash.has(obj)) return hash.get(obj);

    try {
        var result = new obj.constructor();
    } catch (e) {
        result = Object.create(Object.getPrototypeOf(obj));
    }
    hash.set(obj, result)

    let symKeys = Object.getOwnPropertySymbols(obj)
    // 拷贝 Symbol 类型键对应的属性
    if (symKeys.length > 0) {
        symKeys.forEach(symKey => {
            cloneObj[symKey] = isObject(obj[symKey]) ? deepClone(obj[symKey], hash) : obj[symKey]
        })
    }

    if (obj instanceof Map){
        for (let [key,val] of obj){
            result.set(deepClone(key, hash),
            deepClone(val, hash))
        }
    }
    else if (obj instanceof Set){
        for (let val of obj){
            result.add(deepClone(val, hash))
        }
    }

    for(let key of Object.keys(obj)){    
        result[key] = deepClone(obj[key], hash);
    }
    return result
}
```

