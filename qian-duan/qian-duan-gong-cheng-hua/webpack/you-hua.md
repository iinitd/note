# 优化

## 基于 webpack3 的优化

## 基于 webpack4 的优化

新特性： 1. 0配置，开箱即用

### 速度

设置 alias 避免重复寻址

配置 modules，避免递归搜索目录

配置 loader 的 include 和 exclude 范围

webpack-parallel-uglify-plugin

* 去除 unused variables

HappyPack

### 大小

webpack-bundle-analyzer 进行分析

tree-shaking，使用 es6 module，只 import 所需的组件

使用 DLLPlugin 提前打包不变的动态链接库，然后在 webpack配置中通过 externals 引入。

