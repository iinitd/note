# 其他布局

## 自适应正方形

padding-bottom实现,因为padding算百分比是根据父元素的宽度计算的

所以只要width和padding-bottom设置为一样的百分比,就能实现正方形

## 画三角形

* width、height为0
* 三边 border 为透明
* 一边 border 设置为所需要的三角形大小和颜色

```text
demo { width:0; height: 0; border-width: 20px; border-style: solid; border-color: transparent transparent red transparent; }
```

## 画扇形

* width、height为0
* 三边 border-radius 为透明
* 一边 border-radius 设置

```text
demo { width:0; height: 0; border-radius: 20px; border-style: solid; border-color: transparent transparent red transparent; }
```

## hr

```text
hr{
    color:red;
    background-color:red;
    height:1px;
    line-height:1px;/*保证ＩＥ下高度效果一致，不留白色空白区*/
    font-size:0;/*设置为0，以确保IE6下不留空白区*/
    border:none;
}
```

## 元素隐藏

### display:none

* 占用空间消失，本身及其子元素都会消失
* 无法触发事件
* 不影响 CSS3 animation，影响 transition
* 不计入 CSS 计数

### visibility: hidden

* 占用空间还在，子元素默认继承 visibility
* 显式把子元素属性改为 visible，可以实现隐藏父元素，显示子元素
* 支持 animation、transition
* 仍计入 CSS 计数

[https://blog.csdn.net/dkr380205984/article/details/86222101](https://blog.csdn.net/dkr380205984/article/details/86222101)

## -------------------

## css 品字

## css实现一个九宫格

## 一个弹窗+遮罩，写css，设置position:absolute fixed有区别吗？

## H5视差动画

## 九宫格

[https://me.chjiyun.com/2017/12/08/CSS%E5%AE%9E%E7%8E%B0%E8%87%AA%E9%80%82%E5%BA%94%E4%B9%9D%E5%AE%AB%E6%A0%BC%E5%B8%83%E5%B1%80/](https://me.chjiyun.com/2017/12/08/CSS%E5%AE%9E%E7%8E%B0%E8%87%AA%E9%80%82%E5%BA%94%E4%B9%9D%E5%AE%AB%E6%A0%BC%E5%B8%83%E5%B1%80/)

```text
.square{
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 100%; /* padding百分比是相对父元素宽度计算的 */
  margin-bottom: 30px;
}
.square-inner{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%; /* 铺满父元素容器，这时候宽高就始终相等了 */
}
.square-inner>li{
  width: calc(98% / 3);  /* calc里面的运算符两边要空格 */
  height: calc(98% / 3);
  margin-right: 1%;
  margin-bottom: 1%;
  overflow: hidden;
}
```

```text
.flex{
  display: flex;
  flex-wrap: wrap;
}
.flex>li{
  flex-grow: 1; /* 子元素按1/n的比例进行拉伸 */
  background-color: #4d839c;
  text-align: center;
  color: #fff;
  font-size: 50px;
  line-height: 2;
}
.flex>li:nth-of-type(3n){ /* 选择个数是3的倍数的元素 */
  margin-right: 0;
}
.flex>li:nth-of-type(n+7){  /* 选择倒数的三个元素，n可以取0 */
  margin-bottom: 0;
}
```

```text
宽度 30%
justify-content：space-around 空白自动分配
flex-wrap: wrap自动换行；
```

## 两栏

### float

```text
div{
  height:500px;
}
aside{
  width:300px;
  background-color:yellow;
  float:left;
}
main{
  background-color:aqua;
  margin-left:300px;
}
```

### 负 margin

```text
div{
  height:500px;
}
aside{
  width:300px;
  background-color:yellow;
  float:left;
  margin-right:-100%;
}
main{
  width:100%;
  float:left;
}
content{
  margin-left:300px;
  background-color:aqua;
}
```

### flex

```text
container{
    display:flex;
}
aside{
    flex:0 0 200px;
}
main{
    flex: 1 1;
}
```

## 三栏

### 浮动+负外边距（双飞翼布局）

```text
html,body{
  margin:0;
  padding:0;
  height:100%;
}
div{
  height:100%;
}

main{
  background-color:aqua;
  width:100%;
  float:left;
}
left{
  width:200px;
  background-color:yellow;
  float:left;
  margin-left:-100%;
}

right{
  width:300px;
  background-color:orange;
  float:left;
  margin-left:-300px;
}

content{
  margin-left:200px;
  margin-right:300px;
}
```

### 圣杯

此时为container设置一个左右的padding，分别为left和right的宽度。此时整体都是向中间压缩的： 然后对left与right使用position:relative，使其相对现在的位置分别向左、向右移动，从而占据container利用padding空出来的位置。

区别：双飞翼中间还需要多套一个div来装内容。圣杯通过padding+相对定位来保证内容不被覆盖。

### flex

```text
.container{
  height: 100%;
  display: flex;
  flex-direction: column;
  text-align: center;
}
.flex-item:nth-child(1), flex-item:nth-child(3){
  flex-grow:0;
  flex-shrink: 0;
  background-color: #ababab;
}
.flex-item:nth-child(2){
  flex-grow:1;
  flex-shrink: 1;
  background-color: #000;
  overflow-y: auto;
}
```

## 三角形

```text
div {
        width: 0;
        height: 0;
        border-top: 40px solid transparent;
        border-left: 40px solid transparent;
        border-right: 40px solid transparent;
        border-bottom: 40px solid #ff0000;
    }
```

