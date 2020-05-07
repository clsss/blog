---
title: '移动端_性能优化'
date: '2017-09-25T23:12:11+08:00'
publishdate: '2017-09-25T23:54:11+08:00'
lastmod: '2017-09-25T23:54:11+08:00'
draft: false
tags: ['mobile', 'web']
series: ['web']
categories: ['mobile']
img: 'images/blog/img.jpg'
toc: true
summary: 'This is an example'
---

## scroll

实际上，Safari 真的用了原生控件来实现，对于有-webkit-overflow-scrolling 的网页，会创建一个 UIScrollView，提供子 layer 给渲染模块使用
IOS（safari）：

```bash
-webkit-overflow-scrolling: touch; // 弹簧效果
```

安卓：

```bash
overflow:auto; // winphone8 和 android4+
```

bug： 当你给一个元素设置过 position:absolute;或者 position:relative;后再增加-webkit-overflow-scrolling: touch;属性后，你会发现，滑动几次后可滚动区域会卡主，不能在滑动，这时给元素增加个 z-index 值就可以了。

## 关于动画性能

http://www.infoq.com/cn/articles/javascript-high-performance-animation-and-page-rendering

## 回流与重绘

1. 当 render tree 中的一部分(或全部)因为元素的规模尺寸，布局，隐藏等改变而需要重新构建。这就称为回流(其实我觉得叫重新布局更简单明了些)。每个页面至少需要一次回流，就是在页面第一次加载的时候。
2. 当 render tree 中的一些元素需要更新属性，而这些属性只是影响元素的外观，风格，而不会影响布局的，比如 background-color。则就叫称为重绘。
   注：从上面可以看出，回流必将引起重绘，而重绘不一定会引起回流。
   任何对 render tree 中元素的操作都会引起回流或者重绘，比如
   添加、删除元素(回流+重绘)
   隐藏元素，display:none(回流+重绘)，visibility:hidden(只重绘，不回流)
   移动元素，比如改变 top,left(jquery 的 animate 方法就是,改变 top,left 不一定会影响回流)，或者移动元素到另外 1 个父元素中。(重绘+回流)
   对 style 的操作(对不同的属性操作，影响不一样)
   还有一种是用户的操作，比如改变浏览器大小，改变浏览器的字体大小等(回流+重绘)
