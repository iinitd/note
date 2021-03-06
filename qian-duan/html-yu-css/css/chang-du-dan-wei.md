# 前端开发中的「长度单位」们

## 绪论

出厂

三元素组

原始分辨率：显示器横向、纵向的三元素组个数。

设置分辨率：用户通过系统调整的分辨率。

设备像素：与设置分辨率有关，设置分辨率为原始分辨率时，设备像素为一个三元素组的尺寸，设置分辨率小于原始时，设备像素为多个三元素组的尺寸。对于手机来说，分辨率一般不修改，通常可认为设备像素就是三元素组的尺寸。

设备独立像素。

目标：让不同的设备的 1px 「看起来」差不多。

参考像素：

css 像素（px）：

对于高分辨率设备（打印机），css 像素是固定值，1px就等于96分之一英寸，约等于0.2646mm。

对于低分辨率设备（屏幕），css 像素是最接近参考像素的整数个设备像素。

设备像素（dp）：固定值，

DPR = 设备像素 / 设备独立像素

CSS像素 =设备独立像素 = 逻辑像素

1px = \(dpr\)^2 \* 1dp

Flexible方案已不是什么神秘的方案了，借助JavaScript来动态修改meta标签中viewport中的initial-scale的值，然后根据dpr修改html中的font-size值，再使用rem来处理。

viewport

```text
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">

750px + 1/dpr 实现高清：<meta name="viewport" content="width=750px, initial-scale=0.5, maximum-scale=1.0, user-scalable=no">
```

比如把我们的html的font-size设置成1px，这样1rem就等于1px，因为我们标注稿750px，是基于二倍屏的，1个css单位等于2个实际单位，所以我们的font-size设置为0.5px，这样我们设置尺寸时，rem和标注稿的px，就是1比1映射的。

设计稿：750px，其中有一个20px的按钮，1px边框

基准屏幕：DIP 750，根fontSize=100px=1rem，按钮 20px=0.2rem，边框 1px

普通屏幕：DP 375，DIP 375，根fontSize：375/750\*100=50px，375px=7.5rem，按钮 20/100=0.2rem=10px，边框 1px

iphone6：DP 750，DIP 375，根fontSize：375/750\*100=50px，375px=7.5rem，按钮 20/100=0.2rem=10px，边框 1px

scale1：按钮显示为20px的大小，边框显示为2px大小

scale 0.5：按钮显示为10px的大小，边框显示为1px大小

iphone6plus：DP 1242，DIP 414，根fontSize：414/750\*100=55.2px，414px=7.5rem，按钮 20/100=0.2rem=11.04px

scale1：显示为3倍大小

scale 0.333：按钮显示为11.04px的大小，边框显示为1px大小

```text
   function changeRootFont() { 
     var designWidth = 750, rem2px = 100;
     document.documentElement.style.fontsize =
     ((window.innerWidth / designWidth) * rem2px) + 'px'; 
    //iphone6: (375 / 750) * 100 + 'px';
   };
```

```text
(function (doc, win) {
    var docEl = doc.documentElement,
            resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
            recalc = function () {
                var clientWidth = docEl.clientWidth;
                if (!clientWidth) return;
                clientWidth = clientWidth>750?750:clientWidth;//防止被放大保证宽屏效果
                document.querySelector('meta[name=viewport]').setAttribute('content','width=750, initial-scale='+clientWidth/750+', maximum-scale=1.0, user-scalable=0');
                document.querySelector('body').setAttribute('style','visibility:visible;');
            };
    if (!doc.addEventListener) return;
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);
```

rem 基准的两种常见方案

## rem 模拟 vw

设计稿 750px，按钮20px，边框1px

DIP 750，**rfs 75px**，按钮 20/75=0.266666rem

DIP 375，rfs 37.5px，按钮 20/75=0.266666rem

## vw 方案

设计稿 750px，按钮20px，边框1px

DIP 750px，那么 1vw = 7.5px

DIP 750，按钮 20/7.5=2.66666vw

DIP 375，rfs 37.5px，按钮 20/7.5=2.66666vw

## rem:px 1:100方案

设计稿 750px，按钮20px，边框1px

DIP 750，**rfs 100px**，按钮 20/100=0.2rem

DIP 375，rfs 50px，按钮 20/100=0.2rem

[https://github.com/jawil/blog/issues/21](https://github.com/jawil/blog/issues/21) [https://xgfe.github.io/2018/01/26/zhoushiqi/Web%E7%AB%AF%E5%B1%8F%E5%B9%95%E9%80%82%E9%85%8D%E6%96%B9%E6%A1%88/](https://xgfe.github.io/2018/01/26/zhoushiqi/Web%E7%AB%AF%E5%B1%8F%E5%B9%95%E9%80%82%E9%85%8D%E6%96%B9%E6%A1%88/) [https://www.cnblogs.com/yelongsan/p/7975580.html](https://www.cnblogs.com/yelongsan/p/7975580.html) [https://yanni4night.github.io/layout/2015/12/30/dpr-scale-rem.html](https://yanni4night.github.io/layout/2015/12/30/dpr-scale-rem.html) [https://juejin.im/post/5ec3c6de6fb9a048021457ea](https://juejin.im/post/5ec3c6de6fb9a048021457ea) [https://github.com/amfe/article/issues/17](https://github.com/amfe/article/issues/17) [http://caibaojian.com/flexible-js.html](http://caibaojian.com/flexible-js.html) [https://github.com/jawil/blog/issues/21](https://github.com/jawil/blog/issues/21)

