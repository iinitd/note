# 执行上下文

执行上下文

## 基本概念

ES3：scope、VO、this

ES5：词法环境、变量环境、this

ES2018：词法环境（获取变量或 this 时使用）、变量环境（声明变量时使用）、code evaluation state、function、scriptOrModule、Realm、Generator

词法环境：用来存储映射关系的对象。

EnvironmentRecord：具体变量值或函数在内存（堆）里的地址。

outer：外层环境的引用地址

变量环境：存储 var 声明变量

闭包：绑定了执行环境的函数。

## 什么是执行上下文

是为代码段评估和执行创建的对象。用来跟踪代码执行情况、当前执行函数、作用域、this、变量映射。

包括三种类型。

* 全局执行上下文：程序启动时创建。一个程序只会有一个。所有不在函数里面执行的代码都在全局上下文中。创建 window 对象，绑定 this 到 window。
* 函数执行上下文：函数被调用时创建。
* Eval 执行上下文：调用 eval 时创建。

执行上下文创建之后被压入执行栈的栈顶。执行结束之后出栈。

## 执行上下文的创建

发生在预解析阶段。

### 创建词法环境

#### 创建 outer 的指向

#### 创建环境记录

对于**函数上下文**，创建声明环境记录器 DER。

* 创建 arguments
* 创建函数声明
* 创建顶层块级作用域的 let const 声明，初始值为未初始化，并禁止访问。（极客时间：浏览器工作原理 09）

对于**全局上下文**，创建对象环境记录器 OER。

* 关联内建对象 Object/Array 等。
* 创建全局对象 window/global。
* 创建全局变量声明。

对于**块级作用域**的机制，是在语法环境中维护了一个栈。

* 预解析阶段，创建函数第一层的 let/const 声明，压入栈顶。
* 执行阶段，走到对应的代码块，将对应的 let/const 声明，压入栈顶。
* 取值时，沿着词法环境栈顶往下找，没有的话再去找变量环境，再没有就沿着 outer 找。

### 创建变量环境

创建 var 声明，初始值为 undefined。

### this 绑定

对于**全局上下文**，指向 window/global。

对于**函数上下文**，取决于调用形式。

先拿到 MemberExpression，把他叫作ref。 如果 ref 不是 Reference，则指向 undefined。 如果 ref 是 Reference，则看 base。

* base 是对象，则 this 为 GetBase\(ref\)，即该对象。
* base 是 ER，则 this 为 ImplicitThisValue\(ref\)，即 undefined。

  参考：[https://github.com/mqyqingfeng/Blog/issues/7](https://github.com/mqyqingfeng/Blog/issues/7)

对象调用：this 指向该对象。 直接调用：this 指向全局对象，严格模式指向 undefined。 call/apply/bind：指向第一个参数。 构造函数：指向被创建的实例。 DOM 事件回调：this == e.target。 箭头函数：外层作用域的 this。

## 常见问题解释

arguments 到底是哪里来的？

函数表达式和函数声明的区别

变量提升到底是什么？

```text
是创建执行上下文时候发生的行为，在预解析阶段。
只是提升声明，不会进行赋值。
执行到赋值语句之前使用右值，会有不同行为：
    - 对于 var 会得到 undefined。
    - 对于 let const 会报错未初始化（该现象被称为「暂时性死区」）
```

