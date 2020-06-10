# 迭代器 Iterator

* 基本概念
* 目的是提供一个统一的遍历访问机制。
* 本质是一个具有next方法的指针对象。
* 对象可遍历 === 对象具有 Symbol.iterator 属性，即迭代器生成器（返回值为迭代器的函数）。
* next 接口用于访问下一个元素。
* 返回IterationResult 的形式为 {value:xxx, dont:true/false}.
* 已内置迭代器的原生对象有：数组、Set、Map、类数组对象（如 arguments 对象、DOM NodeList 对象）、Generator 对象、字符串。注意：不包括普通对象。
* 消费迭代器的接口
* for value of array

```text
- 注意区别：for of 和 for in「可遍历」和「可枚举」的区别。
```

```text
- for index in array 是遍历「可枚举属性」。
```

* \[...array\]
* let \[x,y\] = array
* Array.from\(array\)
* 遍历普通对象，需要配合 Object.keys\(\) 使用。

