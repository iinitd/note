# 垂直居中

绝对定位+top+left+margin

```text
.element {
    width: 600px; height: 400px;
    position: absolute; left: 50%; top: 50%;
    margin-top: -200px;    /* 高度的一半 */
    margin-left: -300px;    /* 宽度的一半 */
}
```

绝对定位+top+left+transform

```text
.element {
    position: absolute; left: 50%; top: 50%;
    transform: translate(-50%, -50%);    /* 50%为自身尺寸的一半 */
}
```

绝对定位+各方向0+margin 这应该是应用最广的了,因为各方向都要求0px,各方向都会去拉这个内容框,所以最终他们能拉的距离是平分的.然后各方向拉内容区域,只能在margin上,所以margin要设为auto才方便各个方向去拉内容区域.

```text
.element {
    position: absolute; left: 0; top: 0; right: 0; bottom: 0;
    margin: auto;    /* 有了这个就自动居中了 */
}
```

FLex

```text
.demo_2{
    background: orange;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 300px;
}
.demo_div_2 {
   background: #655;
}
```

## 如何实现水平垂直居中

[https://juejin.im/post/5b9a4477f265da0ad82bf921](https://juejin.im/post/5b9a4477f265da0ad82bf921)

* [https://juejin.im/post/5b94d8965188255c5a0cdc02](https://juejin.im/post/5b94d8965188255c5a0cdc02)

