# 数组

## 函数式编程

* forEach
* map
* some/every
* some\(\)：当内部return true时跳出整个循环
* every\(\)：当内部return false时跳出整个循环
* reduce
* 闭包
* 基本概念：
* 模块模式

### autobind

## Array 常用方法

### splice slice

splice：传入 start、count、多个item，表示从某个位置开始删除几个元素并插入新的元素，会修改原数组，返回被删除的元素。

slice：传入start、end，不会修改原数组，返回截取的数组，左闭右开。

\[\].slice.call\(arguments, start \[, end\]\)

### push pop shift unshift

push / pop 在数组末尾增/删元素

* 原理
* 用对象模拟类数组
* length的更新机制
* unshift / shift 在数组首部增/删元素；

### concat

concat 把一个（或多个）数组和（或）值与原数组拼接，返回拼接后的数组

### sort

sort不传值时的输出 sort\(\)是内部使用了什么算法 时间复杂度是多少 indexOf\(\)的时间复杂度是多少

* 默认输出
* compareFunc：负数，a 在b 前面

### join reverse

join\(\) 把数组的所有元素放入一个字符串。元素通过指定的分隔符进行分隔

reverse\(\) 颠倒数组中元素的顺序

### map

```text
var new_array = arr.map(function callback(element, index, array) {
    // Return value for new_array
}[, thisArg])
```

### filter

```text
var new_array = arr.filter(function callback(element, index, array) {
    // Return true or false
}[, thisArg])
```

### reduce

```text
arr.reduce(function callback(accumulator, currentValue, index, array) {
    // Return value
}[, initialValue])
```

## Fucntion 原型方法

* call
* apply
* bind
* 综合场景题
* 11.js实现fibnacci
* 12.js实现递归promise
* 防抖与节流
* This
* 箭头函数的 this：从自己的作用域链的上一层继承this。
* **内置函数**
* escape/unescape
* isFinite/isNaN
* parseFloat/parseInt
* delete
  * delete B.x后，B.x输出什么
* eval
* JSON
* stringify

```text
- 如何将对象转化为字符串，内部实现原理
```

```text
- https://juejin.im/entry/5994eca0f265da2495176c23
```

* parse

## String 原型方法

```text
function replacer(match, p1, p2, p3, offset, string) {
  // p1 is nondigits, p2 digits, and p3 non-alphanumerics
  return [p1, p2, p3].join(' - ');
}
var newString = 'abc12345#$*%'.replace(/([^\d]*)(\d*)([^\w]*)/, replacer);
console.log(newString);  // abc - 12345 - #$*%
```

