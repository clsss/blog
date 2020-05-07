---
title: '移动端_Web如何调起App'
date: '2018-09-25T23:12:11+08:00'
publishdate: '2018-09-25T23:54:11+08:00'
lastmod: '2018-09-25T23:54:11+08:00'
draft: false
tags: ['mobile', 'web']
series: ['web']
categories: ['mobile']
img: 'images/blog/img.jpg'
toc: true
summary: 'This is an example'
---

## 一. 判断机型（Ios/Android）

```bash
/*
 * 设备检测
 * @return {{isIos:Function, isAndroid:Function}}
 */
export function getBrowserVersion() {
    const ua = navigator.userAgent || navigator.vendor || window.opera
    const uaInfo = {
        ios: /\(i[^;]+;( U;)? CPU.+Mac OS X/i.test(ua),
        android: /Android/i.test(ua) || /Linux/i.test(ua)
    }
    return {
        isIos() {
            return uaInfo.ios
        },
        isAndroid() {
            return uaInfo.android
        }
    }
}
```

## 二. 分类套路

### 1. Android

安卓有 2 种常用方式：
a.自定义 Scheme
b.Chrome Intent

#### 自定义 Scheme

scheme 协议在 app 注册之后，与前端进行统一约定，通过 H5 页面访问某个具体的协议地址，即可打开对应的 app 客户端页面。

```bash
1. 通过a标签打开，点击标签是启动
<a href="m://app.com?openid=123">打开app </a><br/>

2. 通过iframe打开，设置iframe.src即会启动
<iframe src="m://app.com?openid=123"></iframe>

3. 直接通过window.location进行跳转
window.location.href=" m://http://app.com?openid=123"
```

> 微信的白名单限制会导致法通过 scheme 来唤醒本地 app，只有白名单内的 app 才能通过微信浏览器唤醒，等微信出方案好了

#### Chrome Intent

自定义的 scheme 可以搞定很多浏览器，但是 Chrome 除外。原因是为了更有序的打通浏览器页面和本地应用，Chrome 25 后不再支持自定义的 scheme，比如通过设置 window.location.href=" m://http://zyl.com "将无法唤醒本地客户端。

而推出了 Chrome Intent，作为标准协议进行推广，其格式如下：

```bash
intent:
   	HOST/URI-path // Optional host
   	#Intent;
	package=[string];
	action=[string];
	category=[string];
	component=[string];
	scheme=[string];
S.browser_fallback_ur=[encoded_full_url];end;
```

```bash
<a href="intent://zyl.com?arg0=Jason&arg1=Zhao#Intent;scheme=m;package=com.zyl.demo.htmlcallapp1;S.browser_fallback_url=https%3A%2F%2Fwww.baidu.com;end">打开app by Intent</a><br/>
```

### 1. IOS

方式：

1. 自定义 Scheme

2. Universal links
    > Universal links 为 iOS9 上一个所谓通用链接的深层链接特性，一种能够方便的通过传统 HTTP 链接来启动 app，使用相同的网址打开网站和 app；通过唯一的网址，就可以链接一个特定的视图到你的 app 里面，不需要特别的 scheme。

对比 scheme 的方式 Universal links 有以下优点：

1. 通过 scheme 启动 app 时，浏览器会弹出确认框提示用户是否打开，而 Universal links 不会提示，体验更好
2. Universal links 可在微信浏览器中打开外部 app
