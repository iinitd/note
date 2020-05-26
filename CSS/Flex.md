
# CSS3 flex

## 基本概念
由 flex container、flex item 组成。

主轴、交叉轴。

## 容器属性

```
定义主轴方向
flex-direction: row | row-reverse | column | column-reverse;
```

```
定义是否换行
flex-wrap: nowrap | wrap | wrap-reverse;
```

```
定义元素在主轴上的对齐方式
justify-content: flex-start | flex-end | center | space-between | space-around;
```

```
定义元素在交叉轴上的对齐方式
align-items: flex-start | flex-end | center | baseline | stretch;
```

## 元素属性

```
元素在容器中的排序，数字越小越靠前
order: <integer>;
```

```
元素占据的主轴空间
flex-basis: <length> | auto;
flex-basis: 0%; 什么意思
```

```
元素的放大缩小比例
flex-grow: <number>;
flex-shrink: <number>;
正数、负数、0、百分数什么意思？
```

```
……？
flex: 1;
flex: 0;
flex: 0%;
flex: 2 3;
flex: 11 32px;
```

```
元素在交叉轴的对齐方式
align-self: auto | flex-start | flex-end | center | baseline | stretch;
```

https://blog.csdn.net/aliven1/article/details/78853725

# flex 详解

flex: 1
flex-grow: 1; flex-shrink: 1; flex-basis: 0%;

```
总结
这位老父亲根据W3C教义，首先会看孩子们用width/flex-basis表达出的“欲望”总和是不是超过了自己的财产总量。假如财产不够分，就需要确保他的孩子们不会打起来，那么flex-shrink这一“损失容忍度”会被优先照顾，然后再根据各自的“欲望”按比例分配财产；假如财产够分，那么“损失容忍度”会被无视，财产将先满足孩子们的“欲望”，然后剩下的多出来的财产将按照flex-grow“剩余财产分配比例”来分配，所有有此值的孩子不论之前是否已经分到过财产，都会重新再分配一次。另外孙子的欲望可以被父亲的width给束缚，而假如不束缚的话可能爷爷会因为偏爱孙子为了满足孙子的欲望可能会把整个家族都送给孙子。

重点知识点归纳

flex:1本质上是继承，如果父元素不知道自己有多宽，也就无法给子元素分配
flex: 1; width: 1px;可以阻止子元素影响当前元素对于width获取的优先级
flex-shrink: 0可以保证争取到自己所需的宽度，“0容忍，不服就干”
宽度分配先尽量满足所有width/flex-basis，然后把剩余宽度交给flex-grow分配
```