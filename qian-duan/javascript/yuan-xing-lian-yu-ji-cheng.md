# 原型链与继承

## 原型链

实例对象 = new 构造函数\(\)

实例对象.**proto** = 原型对象

构造函数.prototype = 原型对象

原型对象.constructor = 构造函数

构造函数.prototype.constructor = 构造函数

* 只有函数才有 prototype 属性
* 用 Function.prototype.bind 创建的函数对象没有 prototype 属性
* Function._proto_ === Function.prototype

## new

新建一个对象。 赋值原型对象。 使用该对象调用构造函数。 返回「该对象」或者「构造函数返回的对象」。

```text
function mockNew(Con, ...args) {
  let obj = {}
  Object.setPrototypeOf(obj, Con.prototype)
  let result = Con.apply(obj, args)
  return result instanceof Object ? result : obj
}
```

不能与 bind 一起使用。

## 继承

### 演化过程

### 最佳实践

* 继承：[https://github.com/mqyqingfeng/Blog/issues/15](https://github.com/mqyqingfeng/Blog/issues/15)
* [https://juejin.im/post/5dba456d518825721048bce9](https://juejin.im/post/5dba456d518825721048bce9)
* ES6 Class继承原理：[https://juejin.im/post/5bcb2e295188255c55472db0](https://juejin.im/post/5bcb2e295188255c55472db0)
* 基本原则
* 封装性
* 构造函数的 prototype 应该指向原型
* 原型的 constructor 应该指向函数（使得 instanceof 能够工作）
* 子类的属性私有，函数共享（挂载在原型上）

### ES6 类的继承

[https://juejin.im/post/5dba456d518825721048bce9](https://juejin.im/post/5dba456d518825721048bce9)

## 原型链继承

创建父类的实例，赋给子类的原型。

缺点：共享父类实例上的引用变量。

## 借用构造函数继承

父类的方法也写在父类构造函数里面。 子类调用父类构造函数来增强。

缺点：父类方法不能复用。

## 组合继承

父类方法写在父类的原型上。 子类调用父类构造函数来增强。 创建父类的实例，赋给子类的原型。

缺点：调用两次父类的构造函数。

## 寄生组合继承

父类方法写在父类的原型上。 子类调用父类构造函数来增强。 新建空对象，使其原型指向父类原型，赋给子类的原型。

优点：只调用父类构造函数一次，子类原型和父类原型隔离。

```text
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}

Object.create(Parent.prototype)
```

```text
function inheritPrototype(subType,superType){
    var prototype = object(superType.prototype);    //创建对象
    prototype.constructor = subType;    //增强对象
    subType.prototype = prototype;    //指定对象
}
```

第一步创建超类原型对象的一个副本。

第二步为副本添加constructor属性，使其指向subType，弥补因为重写原型而失去默认的constructor属性。

最后一步，将副本赋值给子类型的原型。

整个过程说的简单点，就是将超类原型对象的一个副本复制给子类的原型对象。这样一来就可以避免继承超类的实例属性，也就是避免了在子类原型对象上创建多余的属性了。

