# ES6



## ES6

如何理解ES6，你知道哪些？

## 箭头函数

* 没有自己的this，arguments，super 或 new.target。
* 箭头函数内部使用的 this, arguments，只是引用了封闭作用域内的对应变量。
* 不能作为构造函数，会抛出错误。
* 没有 prototype 属性，返回 undefined。
* 空的箭头函数，返回 undefined。
* [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow\_functions](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

## 解构赋值

* 数组的解构赋值机制是：迭代器。
* 对象的解构赋值机制是：先找到同名属性，然后赋值给前面变量。
* 支持默认值。

