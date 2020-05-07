---
title: '移动端_禁止竖屏'
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

## 禁止竖屏

```bash
(function rotate(){
	var orientation=window.orientation; //当屏幕方向改变时
	if(orientation==90||orientation==-90){
	document.body.style.display='none';
	alert("请使用竖屏访问！");
	}
	window.onorientationchange=function(){
	document.body.style.display="block";
	rotate();
	};
})()
```

## 禁止选择

```bash
<style type="text/css">
.user_select_none {
	-webkit-tap-highlight-color: rgba(0, 0, 0, 0.5);
	-webkit-touch-callout: none;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}
</style>
```
