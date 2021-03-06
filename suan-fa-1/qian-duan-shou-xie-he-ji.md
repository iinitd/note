# 前端手写合集

## 函数 Function.prototype

### call

```text
Function.prototype.myCall = function (context) {
    context = context ? Object(context) : window
    context.fn = this
    let args = [...arguments].slice(1)
    let r = context.fn(args)
    delete context.fn
    return r
}
```

### apply

```text
Function.prototype.myApply = function (context) {
    context = context ? Object(context) : window
    context.fn = this
    let args = [...arguments][1]
    if (!args) {
        return context.fn()
    }
    let r = context.fn(...args)
    delete context.fn;
    return r
}
```

### bind

```text
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}

Function.prototype.bind = function (context) {
    let _me = this
    let bindArgs = [].slice.call(arguments, 1)
    let fBound = function () {
        let fnArgs = [].slice.call(arguments)
        return _me.apply(this instanceof fBound ? this : context, bindArgs.concat(fnArgs))
    }
    fBound.prototype = objectCreate(this.prototype)
    return fBound
}
```

### new

```text
function mockNew(Con, ...args) {
  let obj = {}
  Object.setPrototypeOf(obj, Con.prototype)
  let result = Con.apply(obj, args)
  return result instanceof Object ? result : obj
}
```

## 原型

### instanceof

```text
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
    var O = R.prototype;
    L = L.__proto__;
    while (true) { 
        if (L === null) 
        return false; 
        if (O === L) // 这里重点：当 O 严格等于 L 时，返回true 
        return true; 
        L = L.__proto__; 
    } 
}
```

### 继承

组合寄生继承（最佳方案）

```text
function subType(name, age){
    superType.call(this, name);
    this.age = age;
}

function object(o){
    function F(){};
    F.prototype = o;
    return new F();
} // Object.create(Parent.prototype)

function inheritPrototype(subType,superType){
    var prototype = object(superType.prototype);    //创建对象
    prototype.constructor = subType;    //增强对象
    subType.prototype = prototype;    //指定对象
}
```

组合继承

```text
function Father(name,age) {
  this.name = name
  this.age = age
  this.names = []
}

Father.prototype.getNames = function() {
  return this.names
}

function Son(name, age, job) {
  Father.call(this, name, age)
  this.job = job
}

Son.prototype = new Father()
Son.prototype.constructor = Son
```

## 数组 Array.prototype

### 数组去重

```text
Array.prototype.unique = function () {
  return [...new Set(this)];
}

Array.prototype.unique = function () {
  const tmp = new Map();
  return this.filter(item => {
    return !tmp.has(item) && tmp.set(item, 1);
  })
}

Array.prototype.unique = function () {
  return this.sort().reduce((init, current) => {
    if(init.length === 0 || init[init.length - 1] !== current){
      init.push(current);
    }
    return init;
  }, []);
}

function removeRepeat(arr) {
    return arr.filter((item, index) => arr.indexOf(item) === index)
}
```

### 数组洗牌

```text
function shuffle(arr) {
    let m = arr.length;
    while (m > 1){
        let index = Math.floor(Math.random() * m--);
        [arr[m] , arr[index]] = [arr[index] , arr[m]]
    }
    return arr;
}
```

### 数组扁平化

```text
// 1. 命令式
function fn(array) {
    let res = []
    for (item of array) {
        if (item instanceof Array) {
            res = res.concat(fn(item))
        } else {
            res.push(item)
        }
    }
    return res
}

// 2. 函数式
function fn(array) {
    return array.reduce((prev, item) => {
        return item instanceof Array ? prev.concat(fn(item)) : prev.concat(item)
    }, [])
}

// ES6
function fn(array) {
    while (array.some(item => item instanceof Array)) {
        array = [].concat(...array)
    }
    return array
}
```

### reduce

```text
// API

arr.reduce(function callback(accumulator, currentValue, index, array) {
    // Return value
}[, initialValue])
```

```text
function reduce(param, callback, initVal) {
  var hasInitVal = initVal !== void 0;
  var acc = hasInitVal ? initVal : param[0];
  each(hasInitVal ? param : Array.prototype.slice.call(param, 1), function(v, k, o) {
    acc = callback(acc, v, k, o);
  });
  return acc;
}
```

## 函数式编程

### 柯里化

```text
// ES6
function curry1(fn, ...args) {
    return function partial(...args2) {
        if (args.length + args2.length >= fn.length) {
            return fn(...args, ...args2)
        }
        return partial(fn, ...args, ...args2)
    }
}

// ES5
function curry2(fn) {
    var args = [].slice.call(arguments, 1)
    return function partial() {
        args = args.concat([].slice.call(arguments))
        if (args.length >= fn.length) {
            return fn.apply(this, args)
        }
        return partial
    }
}
```

### 防抖

在某个连续触发的函数时，n秒内没有再被触发，则执行一次。 本质：如果这段时间再发生，重新设定定时器

```javascript
function debounce(fn, delay) {
    var timer
    return function () {
        var context = this
        var args = arguments
        clearTimeout(timer)
        timer = setTimeout(function () {
            fn.apply(context, args)
        }, delay)
    }
}
```

### 节流

指定时间内，只触发一次。 本质：如果这段时间再发生，重新设定定时器，同时记录上一次执行时间，如果大于间隔则立即执行。

```text
function throttle(fn, wait, options) {
    wait = wait || 0;
    var timerId, lastTime = 0;
    function throttled() {
        var currentTime = new Date();
        if (currentTime >= lastTime + wait) {
            fn();
            lastTime = currentTime;
        } else {
            if (timerId) {
                clearTimeout(timerId);
                timerId = null;
            }
            timerId = setTimeout(function () {
                fn()
            }, wait);
        }
    }
    return throttled;
}
```

### pipe/compose

```text
let loopItem = (prevFn, nextFn) => (...args) => prevFn(nextFn(...args))

const compose = (...fns) => fns.reduce(loopItem);
const pipe = (...fns) => fns.reduceRight(loopItem)

const example = pipe(
    (x, y) => x * y,
    x => x + 1
);
console.log(example(3, 4)) // 13
```

## EventEmitter

```text
class NotEvent {
    constructor() {
        this._hub = {}
    }
    on(eventName, cb) {
        let cbs = this._hub[eventName] || []
        cbs.push(cb)
        this._hub[eventName] = cbs
        return this
    }
    trigger(eventName, ...payload) {
        let cbs = this._hub[eventName] || []
        cbs.forEach(cb => {
            cb(...payload)
        })
        return this
    }
    off(eventName, cb) {
        this._hub[eventName] = this._hub[eventName].filter((item) => item != cb)
        return this
    }
    once(eventName, cb) {
        let cbs = this._hub[eventName] || []
        let hub = this
        cbs.push(function _oncecb(...payload) {
            cb(...payload)
            hub.off(eventName, _oncecb)
        })
        this._hub[eventName] = cbs
        return this
    }
}

ev = new NotEvent()

function printOne(arg) {
    console.log("haha, " + arg)
}

ev.once("1", printOne)

ev.on("1", function (a) {
    console.log("1", a)
})

ev.on("1", function (a, b) {
    console.log("1", a, b)
})

ev.trigger("1", "tom", "b", "c")
ev.trigger("1", "jane", "b", "c")
```

## 深拷贝

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

## sleep

```text
function sleep (time) {
  return new Promise((resolve) => setTimeout(resolve, time));
}
```

## promise

```text
class Notpromise {
    constructor(executor) {

        this.state = PENDING
        this.value = undefined
        this.reason = undefined
        this.onfulfilledqueue = []
        this.onrejectedqueue = []

        const resolve = (value) => {
            if (this.state == PENDING) {
                this.state = FULFILLED
                this.value = value
                this.onfulfilledqueue.forEach((onfulfilled) => {
                    onfulfilled(this.value)
                })
            }
        }

        const reject = (reason) => {
            if (this.state == PENDING) {
                this.state = REJECTED
                this.reason = reason
                this.onrejectedqueue.forEach((onrejected) => {
                    onrejected(this.reason)
                })
            }
        }

        try {
            executor(resolve, reject)
        } catch (e) {
            reject(e)
        }
    }

    // then 只负责把 onfulfilled, onrejected 回调注册到队列里，返回一个新的 promise。
    then(onFulfilled, onRejected) {

        let newPromise

        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
        onRejected = typeof onRejected === 'function' ? onRejected : r => { throw r }

        switch (this.state) {
            case PENDING:
                return (newPromise = new Notpromise((resolve, reject) => {
                    this.onfulfilledqueue.push(value => {
                        tryResolvePromise(newPromise, onFulfilled, value, resolve, reject, PENDING)
                    })
                    this.onrejectedqueue.push(reason => {
                        tryResolvePromise(newPromise, onRejected, reason, resolve, reject, PENDING)
                    })
                }))
            case FULFILLED:
                return (newPromise = new Notpromise((resolve, reject) => {
                    setTimeout(() => {
                        tryResolvePromise(newPromise, onFulfilled, this.value, resolve, reject, FULFILLED)
                    })
                }))
            case REJECTED:
                return (newPromise = new Notpromise((resolve, reject) => {
                    setTimeout(() => {
                        tryResolvePromise(newPromise, onRejected, this.reason, resolve, reject)
                    })
                }))
        }
    }
}
```

## LazyMan

```text
class _LazyMan {
  constructor(name) {
    this.tasks = [];
    const task = () => {
      console.log(`Hi! This is ${name}`);
      this.next();
    }
    this.tasks.push(task);
    setTimeout(() => {
      this.next();
    }, 0);
  }

  next() {
    const task = this.tasks.shift(); // 取第一个任务执行
    task && task();
  }

  sleep(time) {
    this._sleepWrapper(time, false);
    return this;                     // 链式调用
  }

  sleepFirst(time) {
    this._sleepWrapper(time, true);
    return this;
  }

  _sleepWrapper(time, first) {
    const task = () => {
      setTimeout(() => {
        console.log(`Wake up after ${time}`);
        this.next();
      }, time * 1000)
    }
    if (first) {
      this.tasks.unshift(task);     // 放到任务队列顶部
    } else {
      this.tasks.push(task);        // 放到任务队列尾部
    }
  }

  eat(name) {
    const task = () => {
      console.log(`Eat ${name}`);
      this.next();
    }
    this.tasks.push(task);
    return this;
  }
}

function LazyMan(name) {
  return new _LazyMan(name);
}
```

## async

```text
function async(generatorFn) {
    let g = generatorFn()
    function asyncHelper(iteratorResult) {
        if (iteratorResult.done) {
            return
        }
        const value = iteratorResult.value
        if (value instanceof Promise) {
            value.then((res, err) => {
                return asyncHelper(g.next(res))
            })
        }

    }
    asyncHelper(g.next())
}


async(function* () {
    try {
        const p1 = yield getSleepTime(1000)
        console.log("do1", p1)
        // throw ("!!!")
        Promise.reject(new Error("Oops!"))
        console.log("do2", p2)
        const p3 = yield getSleepTime(p2)
    } catch (e) {
        console.log(e)
    }
})
```

## commonjs

```text
var fs = require('fs')


function Notmodule() {
    this.exports = {}
    this.id = ""
    this.filename = ""
    this.parent = null
}

Notmodule.prototype._cache = {}

Notmodule.prototype._resolvePath = function () {
    return '.'
}

Notmodule.prototype.load = function (filename, parent, b) {
    if (parent) {
        parent.child = this
    }

    this.parent = parent
    this.id = filename
    this.filename = filename

    var dirname = this._resolvePath()
    var data = fs.readFileSync(filename + ".js")

    var funccontent = 'function inner(notexports, notrequire, notmodule, __filename, __dirname){' + data.toString() + '}'

    eval(funccontent)

    norequire = this.notrequire.bind(this)

    inner(this.exports, norequire, this, filename, dirname)

}

Notmodule.prototype.notrequire = function (filename) {

    if (this._cache[filename]) {
        return this._cache[filename].exports
    }

    var child = new Notmodule()
    child.load(filename, this, true)

    this._cache[filename] = child

    return child.exports

}

var notmodule = new Notmodule()

notmodule.load("index", null, true)
```

## requestAnimationFrame

```text
// requestAninationFrame

(function() {
    var lastTime = 0;
    var vendors = ['webkit', 'moz'];
    for(var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
        window.requestAnimationFrame = window[vendors[x] + 'RequestAnimationFrame'];
        window.cancelAnimationFrame = window[vendors[x] + 'CancelAnimationFrame'] ||    // Webkit中此取消方法的名字变了
                                      window[vendors[x] + 'CancelRequestAnimationFrame'];
    }

    if (!window.requestAnimationFrame) {
        window.requestAnimationFrame = function(callback, element) {
            var currTime = new Date().getTime();
            var timeToCall = Math.max(0, 16.7 - (currTime - lastTime));
            var id = window.setTimeout(function() {
                callback(currTime + timeToCall);
            }, timeToCall);
            lastTime = currTime + timeToCall;
            return id;
        };
    }
    if (!window.cancelAnimationFrame) {
        window.cancelAnimationFrame = function(id) {
            clearTimeout(id);
        };
    }
}());
```

## Set

```text
/**
 * 模拟实现第一版
 */
(function(global) {

    function Set(data) {
        this._values = [];
        this.size = 0;

        data && data.forEach(function(item) {
            this.add(item);
        }, this);
    }

    Set.prototype['add'] = function(value) {
        if (this._values.indexOf(value) == -1) {
            this._values.push(value);
            ++this.size;
        }
        return this;
    }

    Set.prototype['has'] = function(value) {
        return (this._values.indexOf(value) !== -1);
    }

    Set.prototype['delete'] = function(value) {
        var idx = this._values.indexOf(value);
        if (idx == -1) return false;
        this._values.splice(idx, 1);
        --this.size;
        return true;
    }

    Set.prototype['clear'] = function(value) {
        this._values = [];
        this.size = 0;
    }

    Set.prototype['forEach'] = function(callbackFn, thisArg) {
        thisArg = thisArg || global;
        for (var i = 0; i < this._values.length; i++) {
            callbackFn.call(thisArg, this._values[i], this._values[i], this);
        }
    }

    Set.length = 0;

    global.Set = Set;

})(this)
```

## Map

```text
function Mymap() {  //构造函数
    this.init();
}

//初始化函数，创建桶（数组），每个位置都是一个对象，每个对象的属性上设置next属性，并且初始化为null。
Mymap.prototype.init = function () {  
    this.tong = new Array(8);
    for (var i = 0; i < 8; i++) {
        this.tong[i] = new Object();
        this.tong[i].next = null;
    }
};
//添加数据。
Mymap.prototype.set = function (key, value) {
    var index = this.hash(key);        //获取到当前设置的key设置到那个位置上
    var TempBucket = this.tong[index]; //获取当前位置的对象
    while (TempBucket.next) {          //遍历如果当前对象链接的下一个不为空
        if (TempBucket.next.key == key) {  //如果要设置的属性已经存在，覆盖其值。
            TempBucket.next.value = value;
            return;                          //return ,不在继续遍历
        } else {
            TempBucket = TempBucket.next;  //把指针指向下一个对象。
        }

    }
    TempBucket.next = {  //对象的next是null ,添加对象。
        key: key,
        value: value,
        next: null
    }
};

//查询数据
Mymap.prototype.get = function (key) {
    var index = this.hash(key);
    var TempBucket = this.tong[index];
    while(TempBucket){
        if(TempBucket.key == key){
            return TempBucket.value;
        }else{
            TempBucket = TempBucket.next;
        }
    }
    return undefined;
}

//删除数据
Mymap.prototype.delete = function(key){
    var index = this.hash(key);
    var TempBucket = this.tong[index];
    while(TempBucket){
        if(TempBucket.next.key == key){
            TempBucket.next = TempBucket.next.next;
            return true;
        }else{
            TempBucket = TempBucket.next;
        }
    }
}
//看当前属性是否存在
Mymap.prototype.has = function(key){
    var index = this.hash(key);
    var TempBucket = this.tong[index];
    while(TempBucket){
        if(TempBucket.key == key){
            return true;
        }else{
            TempBucket = TempBucket.next;
        }
    }
    return false;
}
//清空这个map
Mymap.prototype.clear = function(){
    this.init();
}
//使设置的属性平均分配到每个位置上，使得不会某个链条过长。
Mymap.prototype.hash = function (key) {
    var index = 0;
    if (typeof key == "string") {
        for (var i = 0; i < 3; i++) {
            index = index + isNaN(key.charCodeAt(i)) ? 0 : key.charCodeAt(i);
        }
    }
    else if (typeof key == 'object') {
        index = 0;
    }
    else if (typeof key == 'number') {
        index = isNaN(key) ? 7 : key;
    } else {
        index = 1;
    }

    return index % 8;
}

var map = new Mymap();    //使用构造函数的方式实例化map

map.set('name','zwq');
map.get('name');
map.has('name);
```

## Axios/XHR

```text
get(url){
        return new Promise((resolve => {
            let xhr = new XMLHttpRequest();

            xhr.onload = function() {
                resolve({
                    data: JSON.parse(xhr.responseText),
                    status: xhr.status,
                    statusText: xhr.statusText
                });
            }
            xhr.open( 'get', url , true );
            //xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded;charset=UTF-8')
            //xhr.withCredentials = true;
            xhr.send();
        }))
    }
```

## Webpack

### file-loader

```text
let loaderUtils = require("loader-utils");
function loader(source) {
//
let filename = loaderUtils.interpolateName(this, '[hash].[ext]', {content: source}) // 根据内容生成一个[hash].[ext]的文件
this.emitFile(filename, source); // 发射文件
return `module.exports="./dist/${filename}"`; //因为生成的图片在 dist 目录下
}
loader.raw = true;
module.exports = loader;
```

### Plugin

```text
class FileListPlugin{
    constructor(options) {
        this.options = options;
    }
    apply(compiler) {
        compiler.hooks.emit.tap('FileListPlugin',function (compilation) {
            let fileList = 'filelist:\n\n';
            for (let filename in compilation.assets) {
                fileList += ('- '+filename+'\n');
            }
            compilation.assets['filelist.md']={
                source() {
                    return fileList;
                },
                size() {
                    return fileList.length
                }
            }
        });
    }
}
module.exports = FileListPlugin;
```

## 枚举

```text
class Color {
    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

Color.RED = new Color('xxx');
Color.GREEN = new Color('green');
Color.BLUE = new Color('blue');

Object.freeze(Color);               // 冻结对象，防止修改

export {Color}
```

## Webpack 打包原理

```text
(function (modules) {
  function require(fileName) {
    const fn = modules[fileName];

    const module = {exports: {}};

    fn(require, module, module.exports);

    return module.exports;
  }

  require('./src/index.js');
})({
  './src/index.js': function (require, module, exports) {
    "use strict";

    var _test = require("./test");

    var _test2 = _interopRequireDefault(_test);

    function _interopRequireDefault(obj) {
      return obj && obj.__esModule ? obj : {default: obj};
    }

    console.log(_test2.default);
  }, './test': function (require, module, exports) {
    "use strict";

    Object.defineProperty(exports, "__esModule", {
      value: true
    });

    var _message = require("./message");

    var _message2 = _interopRequireDefault(_message);

    function _interopRequireDefault(obj) {
      return obj && obj.__esModule ? obj : {default: obj};
    }

    var a = 'hello' + _message2.default;
    exports.default = a;
  }, './message': function (require, module, exports) {
    "use strict";

    Object.defineProperty(exports, "__esModule", {
      value: true
    });
    var b = 'world';

    exports.default = b;
  },
})
```

## escape

```text
var _ = {};

var escapeMap = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#x27;',
    '`': '&#x60;'
};

_.escape = function(string) {
    var escaper = function(match) {
        return escapeMap[match];
    };
    // 使用非捕获性分组
    var source = '(?:' + Object.keys(escapeMap).join('|') + ')';
    console.log(source) // (?:&|<|>|"|'|`)
    var testRegexp = RegExp(source);
    var replaceRegexp = RegExp(source, 'g');

    string = string == null ? '' : '' + string;
    return testRegexp.test(string) ? string.replace(replaceRegexp, escaper) : string;
}
```

