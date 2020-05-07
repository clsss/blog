---
title: 'webpack-ssr'
date: '2017-10-25T20:54:11+08:00'
publishdate: '2017-10-25T20:54:11+08:00'
lastmod: '2017-10-25T20:54:11+08:00'
draft: false
tags: ['webpack', 'web', 'ssr']
series: ['webpack']
categories: ['ssr']
# img: "images/banner/test1.jpg"
toc: true
summary: 'This is an example'
---

## webpack-ssr

#### webpack.server.conf.js
>不用关心测试环境还是正式环境 只需要再服务端能跑起来，不需要压缩等..
```
/*
 * @Author: _collin【collinssem@126.com】
 * @Date: 2018-04-28 09:26:38
 * @Last Modified by: _collin
 * @Last Modified time: 2018-04-28 09:47:53
 * 服务端渲染配置文件
 */

const path = require('path')
const ExtractPlugin = require('extract-text-webpack-plugin')
const webpack = require('webpack')
const merge = require('webpack-merge')
const baseConf = require('./webpack.base.conf')
// const VueServerPlugin = require('vue-server-renderer/server-plugin')

let config

const isDev = process.env.NODE_ENV === 'development'

const plugins = [
  new ExtractPlugin('styles.[Hash:8].css'),
  new webpack.DefinePlugin({
    'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV || 'development'),
    'process.env.VUE_ENV': '"server"'
  })
]

if (isDev) {
    // 这个插件，让我们服务端打包后无.js文件而是.json文件
	// 这个包会帮我们做很多事情
   plugins.push(new VueServerPlugin())
}

config = merge(baseConf, {
  target: 'node', // node端运行
  // entry: path.join(__dirname, '../src/demo/server-entry.js'), // 改成命令行传入
  devtool: 'source-map', // 代码调试
  output: {
    libraryTarget: 'commonjs2', // 代码export出去的入口为commonjs2
    filename: 'main-server.js',
    path: path.join(__dirname, '../server-build')
  },
  // https://segmentfault.com/a/1190000012113011
  // -D的列表,依赖的所有js文件，webpack不打包这些库,如lodash，axios，
  externals: Object.keys(require('../package.json').dependencies),
  module: {
    rules: [
      {
        test: /\.styl/,
        // 这里不直接使用vue-style-loader, 直接打包是因为里面包含dom操作,再node端是无法使用的
        use: ExtractPlugin.extract({
          fallback: 'vue-style-loader',
          use: [
            'css-loader',
            {
              loader: 'postcss-loader',
              options: {
                sourceMap: true
              }
            },
            'stylus-loader'
          ]
        })
      }
    ]
  },
  plugins
})

config.resolve = {
  alias: {
    model: path.join(__dirname, '../client/model/server-model.js')
  }
}

module.exports = config

```