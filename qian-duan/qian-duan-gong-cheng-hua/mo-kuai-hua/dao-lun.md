# 导论

## AMD

```text
// 定义模块
define('modula-a', ['module-c'], function(c) {
    //
    return ...
})

// 依赖模块
require(["module-a", "module-b"], function(a, b) {
    console.log(a, b);
});
```

有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

## CMD

CMD推崇依赖就近，AMD推崇依赖前置。

```text
//AMD
define(['./a','./b'], function (a, b) {

    //依赖一开始就写好
    a.test();
    b.test();
});

//CMD
define(function (requie, exports, module) {

    //依赖可以就近书写
    var a = require('./a');
    a.test();

    ...
    //软依赖
    if (status) {

        var b = requie('./b');
        b.test();
    }
});
```

## CommonJS

CommonJS 加载模块是同步的，所以只有加载完成才能执行后面的操作。像Node.js主要用于服务器的编程，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了 AMD CMD 解决方案。

## ES6 Module

1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
3. 
