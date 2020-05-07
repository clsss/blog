---
title: '移动端_机型判断'
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

1. 判断机型（Ios/Android）

```bash
function getBrowserVersion() {
	var ua = navigator.userAgent || navigator.vendor || window.opera;
	var uaInfo = {
	ios: !!ua.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/i),
	android: ua.match(/Android/i) || ua.match(/Linux/i)
	};
	var browserVersion = {
	isIos: function () {
	return uaInfo.ios;
	},
	isAndroid: function () {
	return uaInfo.android;
	}
	};
	return browserVersion;
}
```

2. 相应的机型发送相应的请求

```bash
if (openId) {
	if (browserVersion.isIos()) {
	window.webkit.messageHandlers.jumpToVerify.postMessage('verifyProspr');
	} else if (browserVersion.isAndroid()) {
	android.jumpPage(-9);
	}
	} else {
	goDownload();
}
```

3. 相应的机型跳转相应的链接

```bash
if (browserVersion.isIos()) {
	location.href = 'http://app.appsflyer.com/id873518909?pid=web&c=index';
} else if (browserVersion.isAndroid()) {
	location.href = 'http://app.appsflyer.com/com.luxy?pid=web&c=index';
}
```

4. 通知客户端修改右上角 feedback 图标

```bash
if (browserVersion.isIos()) {
	window.webkit.messageHandlers.JSMessage_shouldShowFeedbackButton.postMessage();
} else if (browserVersion.isAndroid()) {
	android.forceShowFeedbackOnTitleBar();
}
```

5. 包括电脑跳转链接

```bash
$('.btn').click(function(){
	var ua = navigator.userAgent.toLowerCase();
	var userAgent = navigator.userAgent
	var t = Date.now();
	var info = window.location.search.substr(1);
	var info2 = window.location.search.substr(1);
	if (/ipad|iphone|mac/i.test(ua)){
		if (userAgent.indexOf("Safari") > -1) {
			window.location = "OMGmart://" + info;
			setTimeout("window.location.href = ' ',3000);
		} else {
			alert('请在 safari 中打开次链接')
		}
	} else if (/android/i.test(ua)){
		window.location.href = "";
		window.setTimeout(function(){
		window.location.href = " "; /**_打开 app 的协议，有安卓同事提供_**/
		},3000);
	} else if (/windows/.test(ua)){
		setTimeout("window.location.href = '",3000);//电脑跳转地址
	}
});
```

6. 调起 prospr 头像：

------定义-----------

```bash
var browserVersion = getBrowserVersion();
function getBrowserVersion() {
	var ua = navigator.userAgent || navigator.vendor || window.opera;
	var uaInfo = {
		ios: !!ua.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/i),
		android: ua.match(/Android/i) || ua.match(/Linux/i)
	};
	var browserVersion = {
		isIos: function() {
			return uaInfo.ios;
		},
		isAndroid: function() {
			return uaInfo.android;
		}
	};
	return browserVersion;
}
--------使用-------
if (!pageStatus.isProspr) {
	initPayPage(1);
} else {
	if (browserVersion.isAndroid()) {
		android.openProfile(openid);
	} else if (browserVersion.isIos()) {
		window.webkit.messageHandlers.JSMessage_jumpToProfileByOpenID.postMessage(openid);
	}
}
```
