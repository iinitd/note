# Loader

loader

## 基本概念

loader：webpack 处理其他类型文件的能力，并将它们转换为有效模块，并添加到依赖图中。

loader 的使用

* test：检测处理文件的类型，正则表达式
* use：指定使用哪个 loader

loader 的构成

* 入参：资源文件内容字符串/buffer
* 同步 loader 返回值：处理后的字符串、sourcemap（js 对象）
* 异步 loader 返回值：callback\(err, values\)
* 链式调用，从后往前调用 loader，后一个 loader 传入前一个 loader 结果，第一个 loader 可以输出 sourcemap

为什么是从右到左？函数组合通常有两种方式，一种是从左到右\(类似 unix 的 pipe\)，另外一种是从右到左\(compose\)。webpack loader 采取的是 compose 方式。

* 编写原则：无状态、避免绝对路径、使用 peer dependencies

## 组成

loader 是一个模块导出函数，在正则匹配成功的时候调用，webpack 把文件数组传入进来，在 this 上下文可以访问 loader API。

this.context: 当前处理文件的所在目录，假如当前 Loader 处理的文件是 /src/main.js，则 this.context 就等于 /src。

this.resource: 当前处理文件的完整请求路径，包括 querystring，例如 /src/main.js?name=1。

this.resourcePath: 当前处理文件的路径，例如 /src/main.js。

this.resourceQuery: 当前处理文件的 querystring。

this.target: 等于 Webpack 配置中的 Target

this.loadModule: 但 Loader 在处理一个文件时，如果依赖其它文件的处理结果才能得出当前文件的结果时， 就可以通过 - - - this.loadModule\(request: string, callback: function\(err, source, sourceMap, module\)\) 去获得 request 对应文件的处理结果。

this.resolve: 像 require 语句一样获得指定文件的完整路径，使用方法为 resolve\(context: string, request: string, callback: function\(err, result: string\)\)。

this.addDependency: 给当前处理文件添加其依赖的文件，以便再其依赖的文件发生变化时，会重新调用 Loader 处理该文件。使用方法为 addDependency\(file: string\)。

this.addContextDependency: 和 addDependency 类似，但 addContextDependency 是把整个目录加入到当前正在处理文件的依赖中。使用方法为 addContextDependency\(directory: string\)。

this.clearDependencies: 清除当前正在处理文件的所有依赖，使用方法为 clearDependencies\(\)。

this.emitFile: 输出一个文件，使用方法为 emitFile\(name: string, content: Buffer\|string, sourceMap: {...}\)。

this.async: 返回一个回调函数，用于异步执行。

## 应用实例

### less-loader

```text
let less = require('less');
module.exports = function (source) {
const callback = this.async();
less.render(source, (err, result) => {
callback(err, result.css);
});
}
```

### style-loader

```text
module.exports = function (source) {

let script = (`

let style = document.createElement("style");

style.innerText = ${JSON.stringify(source)};

document.head.appendChild(style);

`);

return script;

}
```

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

### url-loader

```text
//新建 url-loader.js

let loaderUtils = require("loader-utils");

let mime = require("mime"); // 这个插件可以拿到文件类型（不是后缀）

function loader(source) {

let { limit } = loaderUtils.getOptions(this); //拿到用户设置的最小值

if (limit && limit > source.length) { // 比大小

return `module.exports="data:${mime.getType( //这里 data:${mime.getType(这里传的是文件绝对路径)}是 base64 固定写法，常见的比如“data:image/png,data:image/jpeg”...

this.resourcePath

)};base64,${source.toString("base64")}"`; //转换为 base64

} else {

return require("./file-loader").call(this, source); // 不在范围内的话，就执行 file-loader，并且将源码传入

}

return source;

}

loader.raw = true; //字符串转二进制

module.exports = loader;
```

### mini-css-extract-plugin.Loader

```javascript
export function pitch(request){
// 验证参数
// 创建 ChildCompiler
const childCompiler = this._compilation.createChildCompiler(
`${pluginName} ${request}`,
outputOptions
);

// 给 childCompiler 加载 Plugin
new NodeTemplatePlugin(outputOptions).apply(childCompiler);
new LibraryTemplatePlugin(null, 'commonjs2').apply(childCompiler);
new NodeTargetPlugin().apply(childCompiler);
new SingleEntryPlugin(this.context, `!!${request}`, pluginName).apply(
childCompiler
);
new LimitChunkCountPlugin({ maxChunks: 1 }).apply(childCompiler);

// 转换请求 loader 的 loader
childCompiler.hooks.thisCompilation.tap(
`${pluginName} loader`,
(compilation) => {
compilation.hooks.normalModuleLoader.tap(
`${pluginName} loader`,
(loaderContext, module) => {
// eslint-disable-next-line no-param-reassign
loaderContext.emitFile = this.emitFile;

if (module.request === request) {
// eslint-disable-next-line no-param-reassign
module.loaders = loaders.map((loader) => {
return {
loader: loader.path,
options: loader.options,
ident: loader.ident,
};
});
}
}
);
}
);

}
```

