Plugin

# 基本概念

webpack 的事件机制是基于 tapable 实现的。

原理就是在整个构建流程的各个阶段，都预设了许多钩子，开发者可以在指定的阶段加入自己的行为到webpack构建流程中。

# hooks

## compiler hooks

- run：开始前执行
- compiler：创建新的compilation前执行
- compilation：创建后执行
- make：完成一次编译之前执行
- emit：生成文件到output目录前执行，compilation
- afterEmit：生成后执行
- assetEmitted：生成过程中执行，file、info
- done：一次编译完成后执行，stats

## compilation hooks

开始构建module之前触发

compilation.hooks.buildModule.tap('xxx', module => {})

在用optimize优化之前触发的钩子

compilation.hooks.optimize.tap('xxx', () => {})

在生成chunks资源时触发

compilation.hooks.shouldGenerateChunkAssets.tap('xxx', () => {})

在childCompiler配置完毕之后执行

compilation.hooks.childCompiler.tap('xxxx', (childCompiler, comilerName, compilerIndex) => {})

# 基本构成

- 一个 JavaScript 命名函数。
- 绑定 apply 原型方法。
- 方法中注册 webpack hook。
- 处理 webpack 内部实例的特定数据。
- 功能完成后，调用 webpack 提供的回调。

整个webpack流程由compiler和compilation构成,compiler只会创建一次，compilation如果开起了watch文件变化，那么会多次生成compilation. 那么这2个类下面生成了需要事件钩子

```
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