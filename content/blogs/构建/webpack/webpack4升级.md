---
title: 'webpack4升级'
date: '2017-10-25T20:54:11+08:00'
publishdate: '2017-10-25T20:54:11+08:00'
lastmod: '2017-10-25T20:54:11+08:00'
draft: false
tags: ['web', 'webpack4', 'update']
series: ['webpack']
categories: ['webpack4']
# img: "images/banner/test1.jpg"
toc: true
summary: 'This is an example'
---

## webpack4升级

## webpack升级（4.6版）


## 一、dev-server
> 本地运行一个静态服务

```bash
1. 添加了mode，直接区分development和production

2. 插件的替换：
	a. webpack.DefinePlugin（不用了，已经有mode了）
   	b. webpack.CommonsChunkPlugin
    被替代成: optimization配置中配置splitChunks(指定vendor在这个配置里)和runtimeChunk
    c. UglifyJsPlugin --> optimization.minimize设为true
    d. webpack.NamedModulesPlugin，	// webpack默认设置	
	e. webpack.NoEmitOnErrorsPlugin，// 
	f. webpack 4.5.0 以上不支持extract-text-webpack-plugin的[contentHash]路径变量， 即将上位：mini-css-extract-plugin

3. dev-server我改用了webpack-dev-server(原理还是express)取代了express()+ webpack-hot-middleware
4. webpack4支持多种形式的模块类型，但是有时候可能需要加上type进行配合
```

#### package.json:
```bash
"webpack": "4.6.0",

"webpack-cli": "2.0.14",  // 命令行要用webpack-cli这个插件了

"webpack-dev-server": "^3.1.3",	// 没再使用express+中间件的形式启动服务了。直接用这个了

"extract-text-webpack-plugin": "^4.0.0-beta.0", // 这个插件是个坑，webpack提供的第三方api（Tapable.plugin）变成了（.hooks）。这个插件需要使用公测版，才不报错

"html-webpack-plugin": "3.2.0",

npm i -D extract-text-webpack-plugin@next 
还有各种vue-loader，css-loader，style-loader的升级
```

#### webpack.config.js样板
> 具体配置看需求，主要是配置dev/prod：

```bash
webpackConfig = {
    mode: 'development', // development(开发)||production('生产')
    entry: {},
    module: {},
    devtool: '#cheap-module-eval-source-map', // webpack默认会添加，也可以我们指定使用
    // 4.0去掉了CommonsChunkPlugin --> optimization配置
    optimization: {
        splitChunk: {
            // chunks: 'initial',     // 必须三选一： "initial" | "all"(默认就是all) | "async"
            // minSize: 0,            // 最小尺寸，默认0
            // minChunks: 1,          // 最小 chunk ，默认1
            // maxAsyncRequests: 1,   // 最大异步请求数， 默认1
            // maxInitialRequests: 1, // 最大初始化请求书，默认1
            // name: () => {},        // 名称，此选项课接收 function
            cacheGroups: {
                commons: {
                    test: /[\\/]node_modules[\\/]/,
                    name: 'vendor',
                    chunks: 'all'
                },
                // vendor: {                   // key 为entry中定义的 入口名称
                //     chunks: 'initial',        // 必须三选一： "initial" | "all" | "async"(默认就是异步)
                //     test: /react|lodash/,     // 正则规则验证，如果符合就提取 chunk
                //     name: 'vendor',           // 要缓存的 分隔出来的 chunk 名称
                //     minSize: 0,
                //     minChunks: 1,
                //     enforce: true,
                //     maxAsyncRequests: 1,      // 最大异步请求数， 默认1
                //     maxInitialRequests: 1,    // 最大初始化请求书，默认1
                //     reuseExistingChunk: true  // 可设置是否重用该chunk（查看源码没有发现默认值）
                // }
            } // 默认把所有node_modules的代码打到一起
        },
        runtimeChunk: true  // 非entry指定name的时候，自动把runtime相关的代码放到运行时
    },
    plugins: [
        // new webpack.NamedModulesPlugin(), 4.0版本干掉了
        // new webpack.NoEmitOnErrorsPlugin(), 4.0版本干掉了
        // CommonsChunkPlugin, 4.0版本去掉了( optimization配置中设置 )
        
        // https://github.com/ampedandwired/html-webpack-plugin
        new HtmlWebpackPlugin(utils.getHtmlPluginParam(entryInfo)),
        new FriendlyErrorsPlugin() 
    ]
}
```

#### dev-server.js:
- 官方：https://doc.webpack-china.org/configuration/dev-server/#devserver-stats-
```
const devServerOption = {
    publicPath: devConfig.output.publicPath,
    port,
    host: hostName,
    overlay: {
        errors: true // 编译出错的时候，在浏览器页面上显示错误
    },
    // headers: {'Access-Control-Allow-Origin': '*'},
    proxy: proxyTable,
    open: true, // 打开浏览器。// 不知为何没生效，还在调研
    <!--openPage: `/${config.dev.defaultEntryConfig.entryName}.html}`, -->
    // historyApiFallback: {
    //     index: `/src/${config.dev.defaultEntryConfig.entryName}.html`
    // },
    hot: true, // 热更新【已经不需要entry中添加`./build/dev-client?name=${entryInfo.entryName}` hot-reload配置】
    quiet: true // 隐藏控制台打印
 };

// 将server配置写入devConfig
WebpackDevServer.addDevServerEntrypoints(devConfig, devServerOption);
// 编译dev配置
const compiler = webpack(devConfig);
// 启动server
const server = new WebpackDevServer(compiler, devServerOption);

server.listen(port, hostName, function () {
    console.log(chalk.blue(`>>> Listening on ${openUrl}`));
});
```

## 二、build打包
> 测试包/上线

```

```