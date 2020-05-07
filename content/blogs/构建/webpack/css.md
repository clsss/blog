---
title: 'webpack-css处理'
date: '2017-10-25T20:54:11+08:00'
publishdate: '2017-10-25T20:54:11+08:00'
lastmod: '2017-10-25T20:54:11+08:00'
draft: false
tags: ['webpack', 'web']
series: ['webpack']
categories: ['css']
# img: "images/banner/test1.jpg"
toc: true
summary: 'This is an example'
---

## css 处理

#### webpack 是如何引入 css？

1. style-loader（2. 创建 link 标签）
   a. style-loader
   b. style-loader/url（file-loader）
   c. style-loader/useable（ user()，unuser() ）

```bash
options:{
	insertInto: '#app'，	// 插入到app节点
	singleton: true,		// 是否重用
	transform: './css.transform.js'	// 可以做样式的修改
}
```

2. css-loader（1.import css）

```bash
option:{
	minimize: true,		// 压缩css
	modules: true		// 开启css模块化开发
}
```

3. css modules
    > 配置 less/sass

```
rules: [
	{
		test: /\.less$/,
		use:ExtractTextWebpackPlugin.extract([
			{},
			{},
			{
			loader: 'less-loader'
			}
		])
	}
]
```

4. 提取 css 代码 (缓存与共用)
    > 插件：extract-text-webpack-plugin

```
plugins: [
	new ExtractTextWebpackPlugin({
		filename: '[name].min.css'
		allChunks: false,// 指定范围，true 就所有的都提取，默认 false（区分开初始化加载和动态加载）
	})
]
```

在 css-loader 与 less/sass-loader 之间
{
loader: 'postcss-loader',
options:{
ident: 'postcss',
plugins: [
require('postcss-cssnext')()
// 未来的 css 的方法：--mainColor 定义。引用 var(--mainColor)
]
}
}
PostCss（在打包的时候，处理 css 的工具）
Autoprefixer 前缀适配
css-nano 压缩 css（实际上 minimize 就是用了这个）
css-next 语法转换（css Variables,custom selectors,calc()）

```

```
