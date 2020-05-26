Webpack

# 基本概念

* 依赖图：webpack 在处理过程中会生成依赖图，据此映射所需的每个模块，并生成一个或多个 bundle

* entry：构建依赖图的入口，可以指定一个或多个入口

* 多个入口对应多个依赖图。

* 每个 HTML 只使用一个入口。

* 在 webpack4 中不需要为 vendor 添加入口，可以使用 optimization.splitChunks 选项。

* output：输出 bundle 的位置

* loader：webpack 处理其他类型文件的能力，并将它们转换为有效模块，并添加到依赖图中

* plugin：执行范围更广的任务，包括打包优化、资源管理、注入环境变量等/

* 是一个具有 apply 方法的 javascript 对象

* apply 传入 compiler，compiler 可在整个编译生命周期访问

* apply 中调用 webpack 的 compiler hooks，注册回调

* compiler：compiler 对象代表的是配置完备的 Webpack 环境。 compiler 对象只在 Webpack 启动时构建一次，由 Webpack 组合所有的配置项构建生成。

* compilation：compilation 对象代表了一次单一的版本构建和生成资源。当运行 webpack 开发环境中间件时，每当检测到一个文件变化，一次新的编译将被创建，从而生成一组新的编译资源。一个编译对象表现了当前的模块资源、编译生成资源、变化的文件、以及被跟踪依赖的状态信息。编译对象也提供了很多关键点事件回调供插件做自定义处理时选择使用。

# 执行流程

- 将命令行参数与 webpack 配置文件 合并、解析得到参数对象。
- 参数对象传给 webpack 执行得到 Compiler 对象。
- 执行 Compiler 的 run 方法开始编译。每次执行 run 编译都会生成一个 Compilation 对象。
- 触发 Compiler 的 make 方法分析入口文件，调用 compilation 的 buildModule 方法创建主模块对象。
- 生成入口文件 AST(抽象语法树)，通过 AST 分析和递归加载依赖模块。
- 所有模块分析完成后，执行 compilation 的 seal 方法对每个 chunk 进行整理、优化、封装。
- 最后执行 Compiler 的 emitAssets 方法把生成的文件输出到 output 的目录中。

![]({$WP_ASSETS}/076e5d2cfd60121e57870b33df691748.png)

# 打包原理

https://www.cnblogs.com/imwtr/p/9770135.html

webpack 的模块机制包含三大要点：

modules 保存所有模块
__webpack_require__ 函数加载模块
installedModules 对象缓存模块

```
(function(modules){
  加载模块核心代码
})([
  function(module, exports,__webpack_require__) {
    模块0的代码
  },
  function(module, exports,__webpack_require__) {
    模块1的代码
  },
])
```

```
(function(modules) {
    //注册完毕的module (注册过不一定加载完成)
    var installedModules = {};
    function __webpack_require__(moduleId) {
         //检查改module是否已经注册过
        if(installedModules[moduleId])
            return installedModules[moduleId].exports;
        //若没注册过,就新建立一个
        var module = installedModules[moduleId] = {
             exports: {},
             id: moduleId,
             loaded: false
         };

       //绑定this,将module和其引用出去的对象暴露出来,__webpack_require__就是加载其他模块的方法
        modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

        //注册成功
         module.loaded = true;
        //返回其暴露的对象
        return module.exports;
     }
    ...
    return __webpack_require__(0);
})()
```

# 打包

* 体积优化

* 提取第三方库，或者通过外部文件引用使用第三方库

* 代码压缩：uglifyJsPlugin

* 服务器开启 gzip

* 按需加载资源：require.ensure

* 优化 source-map

* css 文件单独打包

* 生产环境去除多余插件

* 效率优化

* 开发环境增量构建，热更新

* 开发环境去除插件，如计算 css hash

* 配置 devtool

* 部分 loader 开启 cache，如 babel-loader

* 第三方库分离或引用

* 提取公共代码

* 指明需要构建和不需要构建的目录，优化搜索

* 只引入需要的部分

* loader
* 本质 #tbd
* npm 查看已有包的命令（我猜 npm ls，居然猜对了。。常写的都知道这个命令是真的其实很少用）
* webpack 的打包原理和各个组成部分，自定义 loader，HMR 原理
* npm install 安装机制
* npm install 安装机制
* 什么是 semver？安装过程？node_modules 目录结构是怎么构建的？
* webpack
* loaders 内部如何实现的
* loaders 执行顺序
* html-webpack-plugin 工作原理，如果要把 js 插入到两个 html 中怎么做
* npm 服务写过吗
* loaders 内部如何实现的
* Loader 可以理解为是模块和资源的转换器，它本身是一个函数，接受源文件作为参数，返回转换的结果，例如可以使用 loader 加载器可以快速编译预处理器(less,sass,coffeeScript)。 Loader 可以在 require()引用模块的时候添加，也可以在 webpack 全局配置中进行绑定，还可以通过命令行的方式使用。
* loaders 执行顺序
* loaders 可以串联，他们应用于管道资源，最后的 loader 将返回 javascript，其它的可返回任意格式（传递给下一个 loader）
* loaders 可以同步也可以异步
* html-webpack-plugin 工作原理，如果要把 js 插入到两个 html 中怎么做
* 将 webpack 中`entry`配置的相关入口 thunk 和 `extract-text-webpack-plugin`抽取的 css 样式 插入到该插件提供的`template`或者`templateContent`配置项指定的内容基础上生成一个 html 文件，具体插入方式是将样式`link`插入到`head`元素中，`script`插入到`head`或者`body`中。
* 配置多个 html 页面
* html-webpack-plugin 的一个实例生成一个 html 文件，如果单页应用中需要多个页面入口，或者多页应用时配置多个 html 时，那么就需要实例化该插件多次；即有几个页面就需要在 webpack 的 plugins 数组中配置几个该插件实例：
* 11.webpack.config 配置
* webpack 怎么实现单页应用多路由
* webpack 页面懒加载
* webpack-dev-server
* webpack 怎么实现单页应用多路由
* webpack 页面懒加载
* 热更新自己写怎么做
* 怎么搭建一个项目的脚手架 多项目管理
* babel polyfill 跟 transform 区别 babel 会有哪些问题
* vue 跟 react 有哪些不同，为什么不选择 weex rn 的方式开发
* loaders 内部是怎么实现的
* babel 是如何将 es6 转成 es5 的
* 2.webpack 打包优化打包效率
* 3.pupteer 和其他自动化工具的比较，如何优化 pupteer 的效率
* 6.webpack 部分 polyfill 合并同文件 按需加载
* 怎么推动标准化的
* 5.如何实现前端插件化
* 9.前端灰度和 ab 测试实现
* commonjs 和 es6 module 的三个区别，webpack 对模块怎么处理
* 模块分包

---

- https://champyin.com/2020/01/12/%E6%8F%AD%E7%A7%98webpack-plugin/
- https://mp.weixin.qq.com/s?__biz=MzI0MTUxOTE5NQ==&mid=2247484030&idx=1&sn=d630d4b3995bbfd50f99e781074acfeb

# hash

hash

compilation的hash值

可以简单理解为 每发生一次变化后打包 都会有一次compilation的hash值

一次变化可以是

js等模块的变化
webpack.config.js的配置内容的变化
compilation对象代表某个版本的资源对应的编译进程

tips:

在file-loader的配置中, hash代表的是该文件的hash
在html-webpack-plugin插件中的属性hash:true 表示的也是 compilation的hash值

chunkhash

在webpack的配置.entry对象中每个模块
而chunkhash就根据js模块的内容 来计算hash值

contenthash

contenthash只有 extract-text-webpack-plugin 中使用

# Tree-shaking

ES6模块依赖关系是确定的，和运行时的状态无关，可以进行可靠的静态分析，这就是tree-shaking的基础。所谓静态分析就是不执行代码，从字面量上对代码进行分析，ES6之前的模块化，比如我们可以动态require一个模块，只有执行后才知道引用的什么模块，这个就不能通过静态分析去做优化。这是 ES6 modules 在设计时的一个重要考量，也是为什么没有直接采用 CommonJS，正是基于这个基础上，才使得 tree-shaking 成为可能，这也是为什么 rollup 和 webpack 2 都要用 ES6 module syntax 才能 tree-shaking。

# chunk bundle module

通过上面的代码知道，module 就是没有被编译之前的代码，通过 webpack 的根据文件引用关系生成 chunk 文件，webpack 处理好 chunk 文件后，生成运行在浏览器中的代码 bundle。

# require.context

一个webpack的api,通过执行require.context函数获取一个特定的上下文,主要用来实现自动化导入模块,在前端工程中,如果遇到从一个文件夹引入很多模块的情况,可以使用这个api,它会遍历文件夹中的指定文件,然后自动导入,使得不需要每次显式的调用import导入模块

# 作用域提升

中可以看出开启 Scope Hoisting 后，函数申明由两个变成了一个，util.js 中定义的内容被直接注入到了 main.js 对应的模块中。 这样做的好处是：

代码体积更小，因为函数申明语句会产生大量代码；
代码在运行时因为创建的函数作用域更少了，内存开销也随之变小。
Scope Hoisting 的实现原理其实很简单：分析出模块之间的依赖关系，尽可能的把打散的模块合并到一个函数中去，但前提是不能造成代码冗余。 因此只有那些被引用了一次的模块才能被合并

# code splitting

你的 Code Splitting = webpack bundle analyzer + CommonsChunkPlugin + 你的分析

我们做 Code Splitting 的目的是为了利用浏览器的缓存，那怎样才能充分利用浏览器的缓存呢？让我们拭目以待。

```
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: ({ resource }) => (
    resource &&
    resource.indexOf('node_modules') >= 0 &&
    resource.match(/\.js$/)
  ),
}),
```