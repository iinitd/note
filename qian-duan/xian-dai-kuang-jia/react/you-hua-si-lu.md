# 优化思路

1. 首页白屏

在 html 骨架中插入 loading 元素 使用 html-webpack-plugin 自动插入 loading

prerender-spa-plugin

1. 动态 polyfill
2. 拆分 chunk

SplitChunksPlugin 采用了完全不同的 heuristics 方法，它会根据模块之间的依赖关系，自动打包出很多很多（而不是单个）通用模块，可以保证加载进来的代码一定是会被依赖到的。

1. tree shaking

配合 es6 module

1. code splitting

动态 import

1. lazy-load

监听 window 对象或者父级对象的 scroll 事件，触发 load； 使用 Intersection Observer API 来获取元素的可见性。

在 HTML 内实现 Loading 态或者骨架屏； 去掉外联 css； 缓存基础框架； 使用动态 polyfill； 使用 SplitChunksPlugin 拆分公共代码； 正确地使用 Webpack 4.0 的 Tree Shaking； 使用动态 import，切分页面代码，减小首屏 JS 体积； 编译到 ES2015+，提高代码运行效率，减小体积； 使用 lazyload 和 placeholder 提升加载体验。

[https://zhuanlan.zhihu.com/p/37148975](https://zhuanlan.zhihu.com/p/37148975)

Vue-Router路由懒加载（import/require） 使用CDN加速 Nginx的gzip压缩 Vue异步组件 服务端渲染SSR 如果使用了UI库，采用按需加载\(babel-plugin-component/import\) 线上关闭sourcemap 如果首屏为登录页，可以做成多入口 Service Worker缓存文件处理 使用link标签的rel属性设置 prefetch（这段资源将会在未来某个导航或者功能要用到，但是本资源的下载顺序权重比较低，prefetch通常用于加速下一次导航）、preload（preload将会把资源的下载顺序权重提高，使得关键数据提前下载好，优化页面打开速度）

