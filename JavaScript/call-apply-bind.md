```
Function.prototype.myCall = function (context) {
    context = context ? Object(context) : window
    context.fn = this
    let args = [...arguments].slice(1)
    let r = context.fn(args)
    delete context.fn
    return r
}



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

```
function mockNew(Con, ...args) {
  let obj = {}
  Object.setPrototypeOf(obj, Con.prototype)
  let result = Con.apply(obj, args)
  return result instanceof Object ? result : obj
}
```

```
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}
```


```
// https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
// Does not work with `new funcA.bind(thisArg, args)`
if (!Function.prototype.bind) (function(){
  var slice = Array.prototype.slice;
  Function.prototype.bind = function() {
    var thatFunc = this
    var thatArg = arguments[0];
    var args = slice.call(arguments, 1);
    if (typeof thatFunc !== 'function') {
      throw new TypeError('bind to nocallable');
    }
    return function(){
      var funcArgs = args.concat(slice.call(arguments))
      return thatFunc.apply(thatArg, funcArgs);
    };
  };
})();
```

