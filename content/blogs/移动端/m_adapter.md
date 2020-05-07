---
title: '移动端——响应式布局'
date: '2017-09-25T23:12:11+08:00'
publishdate: '2017-09-25T23:54:11+08:00'
lastmod: '2017-09-25T23:54:11+08:00'
draft: false
tags: ['mobile', 'web', 'css']
series: ['mobile']
categories: ['web']
img: 'images/blog/img.jpg'
toc: true
summary: 'This is an example'
---

## 一、rem/em 单位

#### rem ( font size of root element)

> 相对长度单位。相对于根元素(即 html 元素)font-size 计算值的倍数

栗子：

```bash
<style>
    html{
        font-size: 12px;
    }
    p {
        font-size: 2rem;
    }
</style>
<body>
    <p>我是html定义的12px的2倍，字体大小为24px</p>
</body>
# 如上如果我们设定html的字体大小为12px，有一段文字设置了font-size：2rem
# 那么意思就是这段文字的font-siez是根元素的2倍，则12*2 = 24px;

# 当然一般情况下，是为了计算方便根的字体会被设置为100px
html{font-size:100px;}
```

#### em

> 相对长度单位。相对于当前对象内文本的字体尺寸。
> 如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。

```bash
<style>
    html{
        font-size: 100px;
    }
    .content{
        font-size: 12px;
    }
    .item {
        font-size: 2em;
    }
</style>
<body>
    <div class="content">
        <p class="item">我是相对于父容器的12px的2倍，字体大小为24px</p>
    </div>
</body>
# 因为默认1em=16px， 1px = 1/16 = 0.0625em ; 100px = 6.25em(625%em)
```

## 二、viewport

```bash
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
```

> 在移动开发时我们都会有上面这段代码

-   viewport :虚拟窗口大小
-   width: 控制 viewport 大小 可以自己设定值如 320px(很少用) 一般设置为 device-width(设备宽度)
-   initial-scale 初始缩放比例 即页面初次加载时的缩放比例默认为 1
-   maximum-scale：用户可缩放到的最大比例
-   dpr(device pixel ratio)

#### 设备的物理像素

-   通俗的讲设备屏幕有多少个可以闪烁的点 是一个具体的概念 比如 iphone6 横向就有 750 个可以改变颜色的点 类 似与电视机 如果家里有 10 年前买的大头电视，你趴在屏幕前仔细看能看到一个个 RGB 的点 这就是设备的物理像素

#### 设备独立像素

```
dpr = 设备物理像素/设备独立像素
```

程序中的 1px 占据设备上的几个最小物理点可以这么理解
iphone3G 设备物理像素 320 个点 设备独立像素 320px 那么 dpr 就是 1
iphone6 设备物理像素 750 个点 设备独立像素 375px 那么 dpr 就是 2
也就是我们 css 中写的 1px 其实不等于设备实际上的那 1px 也可能等于设备上的 2px
根据 dpr 我们就可以灵活的在移动端缩放页面比例
可以通过 window.devicePixelRatio 来获取 dpr

3. 使用 sass:mixin 方法把 px 转成 rem

```
@function px2rem( $px ){
	@return $px*750/$designWidth/20 + rem; //这句是不是感觉很熟悉 这句其实跟上面的那段js是对应的
}
@import 'px2rem.scss';
如果设计图是750：
$designWidth : 750;
.banner{width:px2rem(300)} //如设计稿上的banner是300px 就免去计算环节
```

4. 动态 rem (根据 dpr，计算设置 font-size)

```
(function (doc, win) {
    console.log("dpr:"+win.devicePixelRatio);
    var docEle = doc.documentElement,
    isIos = navigator.userAgent.match(/iphone|ipod|ipad/gi),
    dpr=Math.min(win.devicePixelRatio, 3);
    scale = 1 / dpr,

    resizeEvent = 'orientationchange' in window ? 'orientationchange' : 'resize';
    docEle.dataset.dpr = dpr;

    var metaEle = doc.createElement('meta');
    metaEle.name = 'viewport';
    metaEle.content = 'initial-scale=' + scale + ',maximum-scale=' + scale;
    docEle.firstElementChild.appendChild(metaEle);

    var recalCulate = function  {
        var width = docEle.clientWidth;
        if (width / dpr > 640) {
            width = 640 * dpr;
        }
        docEle.style.fontSize = 20 * (width / 750) + 'px';
    };

    recalCulate
    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvent, recalCulate, false);
})(document, window);
```

获取设备 dpr
算出缩放比例 scale = 1/dpr
创建 meta 以及属性
将 scale 值赋给 initial-scale，maximum-scale
meta 插入到文档中
创建屏幕大小改变重新计算函数并监听

<br><br><br>

========================== 分割线 ==========================
<br><br><br>

以下来自微信公众好前端大全:
主要是总结一下，我在移动端布局用过的方法。
有三种:

-   第一种是响应式布局，利用@meida 判断各个 size；
-   第二种是 REM；
-   第三种是设置 viewport 中的 width。

## 一丶 响应式布局

这种感觉是最好理解了，利用@media 进行断点，在每个断点中编写 css。
@media (max-width:768px){
//css
}
上面这段代码，在浏览器的宽度低于 768 时有效。同理，如果把 max 换成 min，就会变成高于 768 时有效。可以设置宽度，也可以设置高度，也可以同时设置多个值。
在 MDN,@media 上，发现很多值都可以做判断的。宽，高，宽高比，颜色（这个是指定输出设备每个像素单位的比特值），是否横屏或竖屏，还有很多，可以去 MDN 看看。

#### 1.媒体类型:

-   @meida 还可以根据媒体类型进行断点。
-   all，所有设备
-   print，打印
-   screen，彩色的电脑屏幕
-   speech，不知道什么来的。

#### 2.关键字

```bash
@media (max-width:1000px){
    div{background:blue;}
}
@media (min-width:1000px) and (max-width:1150px){
    div{background: yellow;}
}
@media only screen and (max-width:1150px){
    div{border:solid 1px;}
}
@media not print and (max-width:1150px){
    div{border-radius:50%;}
}
```

1. and，就是‘和’的意思，前后两个条件都达到时
2. only，唯一
3. not，除了这个之外 3.按需‘加载’css
4. @media 还可以用在 link 标签上。

```bash
<link rel="stylesheet" href="css/1.css" media="(max-width:500px)"/>
```

## 二丶 REM

REM 这个单位，会根据 html 的 font-size 大小进行转换。

```bash
html{font-size:100px;}
p{padding-top:.5rem;}
```

转换后 p 的 padding-top 就是 50px 了。只要我们进行适当的计算，当前屏幕的宽度，html 的 font-size 是多少 px 就 OK 了。
px <==> rem [文档](http://pxtoem.com/)

#### 1.如何计算当前 html 的 font-size 值

> 当前屏幕宽度 / 750 = 当前屏幕宽度的 font-size / 100

比如，我们拿到的设计稿是 750px，那就设置成当屏幕宽度是 750 的时候，html 的 font-size 就是 100px（当然这个 100px 你可以随意设置的，我设置成 100px 只是方便我计算），然后就根据当前屏幕的宽度 / 750 \* 100，就得到了当前屏幕宽度的 font-size 值。

代码如下

```bash
(function (doc, win) {
    var docEl = doc.documentElement,
    resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',（获取浏览器支持的改变方向的函数，如果'orientationchange'存在，就使用 docEl.style.fontSize 这个改变字体的尺寸）
    recalc = function () {
    var clientWidth = docEl.clientWidth;
    if (!clientWidth) return;
    docEl.style.fontSize = 100 * (clientWidth / 750) + 'px';
};
if (!doc.addEventListener) return;
win.addEventListener(resizeEvt, recalc, false); // resizeEvt 用于这里监听放向改变
doc.addEventListener('DOMContentLoaded', recalc, false); //dom 结构完就开始计算
})(document, window);
```

上面这段代码，是直接根据屏幕宽度进行计算 font-size 的。而淘宝移动端 REM 方案，还根据你的 dpr 去计算，而且会进行整体的缩放。淘宝的这种方案，比上面的代码会好很多。淘宝的方案，解决了另一个问题：边框 1px 的问题。

2.淘宝移动端 REM
这个很简单，只需要加载 js 就好了

```
;
(function(win, lib) {
    var doc = win.document;
    var docEl = doc.documentElement;
    var metaEl = doc.querySelector('meta[name="viewport"]');
    var flexibleEl = doc.querySelector('meta[name="flexible"]');
    var dpr = 0;
    var scale = 0;
    var tid;
    var flexible = lib.flexible || (lib.flexible = {});

    if (metaEl) {
        var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);
        if (match) {
            scale = parseFloat(match[1]);
            dpr = parseInt(1 / scale);
        }
    } else if (flexibleEl) {
        var content = flexibleEl.getAttribute('content');
        if (content) {
            var initialDpr = content.match(/initial\-dpr=([\d\.]+)/);
            var maximumDpr = content.match(/maximum\-dpr=([\d\.]+)/);
            if (initialDpr) {
                dpr = parseFloat(initialDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));
            }
            if (maximumDpr) {
                dpr = parseFloat(maximumDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));
            }
        }
    }

    if (!dpr && !scale) {
        var isAndroid = win.navigator.appVersion.match(/android/gi);
        var isIPhone = win.navigator.appVersion.match(/iphone/gi);
        var devicePixelRatio = win.devicePixelRatio;
        if (isIPhone) {
            // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
            if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {
                dpr = 3;
            } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)) {
                dpr = 2;
            } else {
                dpr = 1;
            }
        } else {
            // 其他设备下，仍旧使用1倍的方案
            dpr = 1;
        }
        scale = 1 / dpr;
    }

    docEl.setAttribute('data-dpr', dpr);
    if (!metaEl) {
        metaEl = doc.createElement('meta');
        metaEl.setAttribute('name', 'viewport');
        metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
        if (docEl.firstElementChild) {
            docEl.firstElementChild.appendChild(metaEl);
        } else {
            var wrap = doc.createElement('div');
            wrap.appendChild(metaEl);
            doc.write(wrap.innerHTML);
        }
    }

    function refreshRem() {
        var width = docEl.getBoundingClientRect().width;
        // 适配平板
        if (width / dpr > 540) {
            width = 540 * dpr;
        }
        var rem = width / 10;
        docEl.style.fontSize = rem + 'px';
        flexible.rem = win.rem = rem;
    }

    win.addEventListener('resize', function() {
        clearTimeout(tid);
        tid = setTimeout(refreshRem, 300);
    }, false);
    win.addEventListener('pageshow', function(e) {
        if (e.persisted) {
            clearTimeout(tid);
            tid = setTimeout(refreshRem, 300);
        }
    }, false);

    if (doc.readyState === 'complete') {
        doc.body.style.fontSize = 12 * dpr + 'px';
    } else {
        doc.addEventListener('DOMContentLoaded', function(e) {
            doc.body.style.fontSize = 12 * dpr + 'px';
        }, false);
    }


    refreshRem();

    flexible.dpr = win.dpr = dpr;
    flexible.refreshRem = refreshRem;
    flexible.rem2px = function(d) {
        var val = parseFloat(d) * this.rem;
        if (typeof d === 'string' && d.match(/rem$/)) {
            val += 'px';
        }
        return val;
    }
    flexible.px2rem = function(d) {
        var val = parseFloat(d) / this.rem;
        if (typeof d === 'string' && d.match(/px$/)) {
            val += 'rem';
        }
        return val;
    }

})(window, window['lib'] || (window['lib'] = {}));
```

然后在 css 中，将 px 转为 rem 就好了。我是用 scss 的，所以写个函数就好了。

```
@function s($px) {
    <a href="http://www.jobbole.com/members/wx1409399284">@return</a> ($px / 75) * 1rem;
}

p{
    font-size:s(40);padding-left: s(52);
}
```

比如 p 的 font-size 在 750 的设计稿是 40px，然后 s(40)就 OK 了。

1. 一些移动端设备的 font-size
   html{font-size:625%}
   @media screen and (min-width:360px) and (max-width:374px) and (orientation:portrait) {
   html { font-size: 703%; }
   }
   @media screen and (min-width:375px) and (max-width:383px) and (orientation:portrait) {
   html { font-size: 732.4%; }
   }
   @media screen and (min-width:384px) and (max-width:399px) and (orientation:portrait) {
   html { font-size: 750%; }
   }
   @media screen and (min-width:400px) and (max-width:413px) and (orientation:portrait) {
   html { font-size: 781.25%; }
   }
   @media screen and (min-width:414px) and (max-width:431px) and (orientation:portrait){
   html { font-size: 808.6%; }
   }
   @media screen and (min-width:432px) and (max-width:479px) and (orientation:portrait){
   html { font-size: 843.75%; }
   }

## 三丶 设置 viewport 中的 width

这种方案，就是定死 viewport 中的 width 大小。
比如设计稿是 750 的，然后就在代码上写：

<meta name="viewport" content="width=750"/>

## 四丶 对比三种方式

响应式的优缺点
优点：兼容性好，@media 在 ie9 以上是支持的，PC 和 MOBILE 是同一套代码的，不用分开。
缺点：要写得 css 相对另外两个多很多，而且各个断点都要做好。css 样式会稍微大点，更麻烦。

REM 优缺点
优点：能维持能整体的布局效果，移动端兼容性好，不用写多个 css 代码，而且还可以利用@media 进行优化。
缺点：开头要引入一段 js 代码，单位都要改成 rem(font-size 可以用 px)，计算 rem 比较麻烦(可以引用预处理器，但是增加了编译过程，相对麻烦了点)。pc 和 mobile 要分开。

设置 viewport 中的 width
优点：和 REM 相同，而且不用写 rem，直接使用 px，更加快捷。
缺点：效果可能没 rem 的好，图片可能会相对模糊，而且无法使用@media 进行断点，不同 size 的手机上显示，高度间距可能会相差很大。

还是看项目需求，再决定使用哪种方案，如果就手机站的话，我使用 REM 比较多。如果对距离比较精确的话，大量 css3 动画的，就使用第三种。


参考：
http://www.cnblogs.com/lyzg/p/4877277.html

