# CSS

【CSS】

## 盒模型

* 组成部分：content、padding、border、margin。
* box-sizing
* 标准盒模型：content-box = content
* IE 怪异盒模型：border-box = content + padding + border

## 文档流

* 普通流。浮动。绝对定位。
* BFC（block format context）
* 格式化上下文
  * 页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。（什么意思？\#tbd）
* 概念：A block formatting context contains everything inside of the element creating it.
* 渲染规则

```text
- 不同的 BFC 不会重叠。
  如果元素被前面的浮动元素盖住了，则可以给该元素创建 BFC。
```

```text
- block 垂直方向一个接一个摆放。
  即普通文档流。
```

```text
- block 垂直方向的距离由 margin 决定，相邻的 margin 重叠。
  经典问题：margin 重叠，以及如何消除。
```

```text
- 子元素的 margin 左侧与包含块 border 左侧重叠。浮动元素遵循该规则。绝对定位不遵循。
  \#tbd
```

```text
- 子元素不会影响到外面的元素。反之也如此。
  BFC 整块区域在或不在，其他元素该怎么布局还是怎么布局。延伸问题：div嵌套，子div是BFC，则父div会发生高度坍缩。​
```

```text
- 计算 BFC 的高度时，浮动元素也参与计算。
  经典问题：高度坍缩。
```

```text
- box 与 包含块的垂直方向 margin，若中间没有border、padding等分隔，则会发生重叠。
  经典问题：父子div margin 重叠。​
```

* 如何形成 BFC

```text
- 浮动元素（元素的 float 不是 none）
```

```text
- 绝对定位元素（元素的 position 为 absolute 或 fixed）
```

```text
- display 为 inline-block、table 类、flow-root
```

```text
- 弹性元素（display为 flex 或 inline-flex元素的直接子元素）
```

```text
- 网格元素（display为 grid 或 inline-grid 元素的直接子元素）
```

```text
- 其他
```

* 如何解决 margin 重叠？

```text
- 使父元素形成 BFC。
```

```text
- 使子元素形成 BFC（会引入新的高度坍缩问题）。
```

```text
- 设置父元素透明边框（使得两个margin中有间隔）。
```

```text
- 在父元素头部插入空的 BFC 伪元素。 `.cf:after，.cf:before {content: ""; display: table;}`
```

* 清除浮动，高度坍缩

```text
- 使用 clear 子元素。（不属于 BFC 应用）
```

```text
- 使用 clearfix 伪元素。（不属于 BFC 应用） `.clearfix:after {clear: both;}`
```

```text
- 使父元素形成 BFC。
```

## 选择器

* 分类
* 标签选择器
* id 选择器
* class 选择器
* 属性选择器
* 伪类选择器
* 伪元素选择器
* 链接选择器
* 行为选择器
* 逻辑选择器
* 伪类和伪元素的区别？

[http://www.alloyteam.com/2016/05/summary-of-pseudo-classes-and-pseudo-elements/](http://www.alloyteam.com/2016/05/summary-of-pseudo-classes-and-pseudo-elements/)

* 伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档数外的元素。因此，伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。
* 优先级
* S：!important
* A：内联
* B：ID
* C：class/属性/伪类
* D：标签/伪元素
* 多个选择器
* .class\_a .class\_b：后代选择器
* .class\_a.class\_b：并
* .class\_a,.class\_b：或

css选择器的解析原则：选择器定位DOM元素是从右往左的方向，这样可以尽早的过滤掉一些不必要的样式规则和元素。

## 块级元素和行级元素

## 属性

### display

* block
* inline
* inline-lock

### position

#### static

普通文档流

#### relative

保留文档流中的位置，只发生视觉偏移 偏移量是相对于自身位置 百分比值是根据包含块 content box 计算

#### absolute

偏移量是相对于「除static以外的第一个祖先元素」的 padding box 百分比是根据包含块 padding box 计算 可以使用 z-index 控制绝对定位框的堆放顺序

参考：[https://juejin.im/post/5d038929f265da1b725bffcc](https://juejin.im/post/5d038929f265da1b725bffcc)

### table-layout

* fixed

### float

* 浮动，直到外边缘碰到包含块或者另一个浮动的边框。
* 设置完浮动的元素，一般都需要设置宽度
* 与 relative 一起设置时，先浮动到相应位置，再根据定位发生偏移
* 与 absolute 一起设置时，浮动失效，以 absolute 为准
* [https://www.cnblogs.com/coffeedeveloper/p/3145790.html](https://www.cnblogs.com/coffeedeveloper/p/3145790.html) \*

### margin

以上只是Margin为正的情况,当Margin则会相反.

Margin参考线和参考物分两类

Top和Left,都是从外往内容推距离,以自身为参考物 Right和Bottom,都是内容框内往外推距离.以临近元素为参考物.例如margin-bottom为-10px时,会把临近的下方元素给提上来10px

## 计量单位

SS计量单位 与font-size相关的单位 ex: 1为 小写x的高度

em: 可以理解为 一个父元素中 font-size的倍数 不一定的话chrome默认为16px 其高度值可以理解为正方形的方格的边长 rem: 跟em类似 但其的参考标准为html标签的font-size \(IE9及其以上浏览器可用, 但是IE9表现有点奇葩\)

ch: 1ch = 0的宽度

与viewpoint相关的单位 vh: 1vh = 1/100的视口高度 vw: 1vw = 1/ 100的视口宽度 vmin: 1vmin = 设备宽度 &gt; 设备高度 ? 1vh : 1vw vmax: 1vmax = 设备宽度 &gt; 设备高度 ? 1vw : 1vh

计量单位的运用

rem和vw的结合 在移动侧 因为移动设备尺寸不一 所以需要对不同尺寸的设备做不一样的大小处理

一般的处理方式是 html{ font-size: xxxxvw } .xxx{ padding: aaarem }

## CSS3 动画

### 变换 transform

```text
transform: translate(50px, 100px);
```

translate：从当前位置移动。 scale：倍数放大当前元素 skew：旋转 matrix：复杂变换

### 过渡 transition

使CSS属性值在一定的时间区间内平滑的过渡。

```text
transition:width 2s ease 0s;
transition:property duration timing-function delay;
```

Animation和transition大部分属性是相同的，他们都是随时间改变元素的属性值，他们的主要区别是transition需要触发一个事件才能改变属性， 而animation不需要触发任何事件的情况下才会随时间改变属性值，并且transition为2帧，从from .... to，而animation可以一帧一帧的。

transition 规定动画的名字 规定完成过渡效果需要多少秒或毫秒 规定速度效果 定义过渡效果何时开始 animation 指定要绑定到选择器的关键帧的名称

### 动画 Animation

> 前端创建动画的方式有三种，分别是 定时器, requestAnimationFrame 以及 CSS3 Animation。

```text
animation: name duration timing-function delay iteration-count direction;
```

```text
.heart {
  animation: heartbeat cubic-bezier(0.2, 0.73, 0.71, 0.44) infinite;
}

@keyframes heartbeat {
  0% {
    transform: scale(1.1);
  }
  50% {
    transform: scale(0.8);
  }
  100% {
    transform: scale(1.1);
  }
}
```

监听动画开始结束：animationstart。animationend。

duration 设置为 infinite 可以使其循环播放。

timing-function 平滑的曲线，steps

[https://juejin.im/post/5ca59688f265da30c3478e19](https://juejin.im/post/5ca59688f265da30c3478e19)

### 多列

```text
column-count/gap/rule
```

### 硬件加速

原理：使其触发合成。

执行到时创建：

transform opacity filter will-change：哪一个属性即将发生变化，进而进行优化。

渲染时创建：

transform: translateZ\(0\); transform: rotateZ\(360deg\);

### 实现九宫格

## CSS3 grid

* 实现

## less/sass

