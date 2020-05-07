---
title: "前端构建分享（入门篇）"
date: "2017-10-25T20:54:11+08:00"
publishdate: "2017-10-25T20:54:11+08:00"
lastmod: "2017-10-25T20:54:11+08:00"
draft: false
tags: ["vue-cli", "web", "vue"]
series: ["web"]
categories: ["vue-cli"]
# img: "images/banner/test1.jpg"
toc: true
summary: "This is an example"
---


## vue-cli
```
前提 
node.js, vue-cli

全局安装脚手架	
npm install -g -vue-cli
			
你需要了解npm/yarn的相关知识，还有淘宝镜像cnpm（主要是用在资源被墙）
尤其是：		
cnpm install node-sass sass-loader -D

新建一个vue 项目流程：				
vue init webpack  <项目名称>	
cd ..
npm install
npm run dev

```
> 注意：要使用npm包管理器就必须安装node（原因：包含关系 ———— nodejs中含有npm，安装好nodejs后，你打开cmd输入npm -v会发现npm的版本号，说明npm已经安装好）
node.js是javascript的一种运行环境，是对Google V8引擎进行的封装。是一个服务器端的javascript的解释器。

> 其实npm是nodejs的包管理器（package manager）。我们在Node.js上开发时，会用到很多别人已经写好的javascript代码，如果每当我们需要别人的代码时，都根据名字搜索一下，下载源码，解压，再使用，会非常麻烦。于是就出现了包管理器npm。

---

<br/>
<br/>

==如果我们不使用vue-cli生成一个vue的项目？==

<br/>
<br/>


## webpack构建

### 一、安装
``` bash
// 全局安装
npm install -g webpack
// 安装到你的项目目录
npm install --save-dev webpack
```

### 二、初始化项目
- 1.创建一个package.json文件(npm说明文件)

``` bash
// 创建package.json
// npm说明文件, 里面蕴含了丰富的信息，包括当前项目的依赖模块，自定义的脚本任务等等
npm init
```

- 2.在本项目中安装Webpack作为依赖包

``` bash
// 安装Webpack
npm install --save-dev webpack
```

- 3.创建运行文件
```
// index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Webpack Project</title>
  </head>
  <body>
    <div id='root'>
    </div>
    <script src="bundle.js"></script>
  </body>
</html>
```

```
// app/main.js;
function createDom() {
  var dom = document.createElement('div');
  dom.textContent = "Hello World !";
  return dom;
};
document.querySelector("#root").appendChild(createDom());
```


### 三、运行webpack
``` bash
// 全局執行
webpack app/main.js dist/bundle.js
// 局部執行
node_modules/.bin/webpack app/main.js dist/bundle.js
```

> 缺点：不太方便且容易出错的



<br/>

---

<br/>


## 更好的webpack运行方式

> 优点：定义一个配置文件，这个配置文件其实也是一个简单的JavaScript模块

```
// 新建: build/webpack.config.js

module.exports = {
  entry:  resolve("app/main.js"), // 入口文件
  output: {
    path: resolve("dist"), // 打包后的文件存放的地方
    filename: "bundle.js" // 打包后输出文件的文件名
  }
}

```
```
// 运行：
webpack build/webpack.config.js
```

<br/>

---

<br/>


## 更优雅的方式：
> 在package.json中对scripts对象进行

```
// package.json
{
  "name": "project-name",
  "version": "1.0.0",
  "description": "a webpack project",
  "scripts": {
    "dev": "webpack build/webpack.config.js" 
  },
  "license": "ISC",
  "devDependencies": {
    "webpack": "3.10.0"
  }
}

// webpack.config.js
module.exports = {
  entry:  resolve("app/main.js"),//已多次提及的唯一入口文件
  output: {
    path: resolve("dist"),//打包后的文件存放的地方
    filename: "bundle.js"//打包后输出文件的文件名
  }
}
```


```
// 构建目录
├─build     
│   │
│   └─webpack.config.js  // webpack配置        
│
├─app     
│   │
│   └─main.js 
│
└─index.html 

```

<br/><br/>

---

<br/><br/>


## Webpack的强大功能

#### 1. 生成Source Maps（使调试更容易）
> 方便的调试能极大的提高开发效率，不过有时候通过打包后的文件，你是不容易找到出错了的地方，对应的你写的代码的位置

```
// webpack.config.js
module.exports = {
  devtool: 'eval-source-map', // 添加
  entry: resolve("/app/main.js"),
  output: {
    path: resolve("/dist"),
    filename: "bundle.js"
  }
}
```

| devtool选项                  | 配置结果                                                                                                                                                                                                                                              |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| source-map                   | 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包速度；                                                                                                                                                  |
| cheap-module-source-map      | 在一个单独的文件中生成一个不带列映射的map，不带列映射提高了打包速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；                                                                                     |
| eval-source-map              | 使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。在==开发阶段==这是一个非常好的选项，在生产阶段则一定不要启用这个选项； |
| cheap-module-eval-source-map | 这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；                                                                                                    |


#### 2. 使用webpack构建本地服务器

> 让你的浏览器监听你的代码的修改，并自动刷新显示修改后的结果，其实Webpack提供一个可选的本地开发服务器，这个本地服务器基于node.js构建，可以实现你想要的这些功能，不过它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖

``` bash
npm install --save-dev webpack-dev-server
```

```
// webpack.config.js
module.exports = {
  devtool: 'eval-source-map', // 生成map
    
  entry: resolve("app/main.js"),
  output: {
    path: resolve("dist"),
    filename: "bundle.js"
  },

  devServer: {
    contentBase: "./dist", // 本地服务器所加载的页面所在的目录
    historyApiFallback: true,// 不跳转(去掉url上的hash：#)
    inline: true // 实时刷新
  }
}
```

```
// package.json
"scripts": {
    "dev": "webpack build/webpack.config.js",
    "server": "webpack-dev-server --open"
},
```
| devserver 配置选项 | 功能描述                                                                                                                                          |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| contentBase        | 默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“public"目录） |
| port               | 设置默认监听端口，如果省略，默认为8080                                                                                                            |
| inline             | 设置为true，当源文件改变时会自动刷新页面                                                                                                          |
| historyApiFallback | 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html                                                   |


#### 3. Loaders
> Loaders是webpack提供的最激动人心的功能之一了。通过使用不同的loader，webpack有能力调用外部的脚本或工具，实现对不同格式的文件的处理，比如说分析转换scss为css，或者把下一代的JS文件（ES6，ES7)转换为现代浏览器兼容的JS文件，对React的开发而言，合适的Loaders可以把React的中用到的JSX文件转换为JS文件。


1. test：一个用以匹配loaders所处理文件的拓展名的正则表达式（必须）
2. loader：loader的名称（必须）
3. include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
4. query：为loaders提供额外的设置选项（可选）

#### 4. bable
> Babel其实是几个模块化的包，其核心功能位于称为babel-core的npm包中，webpack可以把其不同的包整合在一起使用，对于每一个你需要的功能或拓展，你都需要安装单独的包（用得最多的是解析Es6的babel-env-preset包和解析JSX的babel-preset-react包,解析vue的vue-loader）

1. 让你能使用最新的JavaScript代码（ES6，ES7...），而不用管新标准是否被当前使用的浏览器完全支持
1. 让你能使用基于JavaScript进行了拓展的语言，比如React的JSX
1. vue-loader:
1.     ES2015 默认支持;
1.     允许对 Vue 组件的组成部分使用其它 Webpack loaders，比如对 <style> 使用 SASS 和对 <template> 使用 Jade；
1.     .vue 文件中允许自定义节点，然后使用自定义的 loader 处理他们；
1.     把 <style> 和 <template> 中的静态资源当作模块来对待，并使用 Webpack loaders 进行处理；
1.     对每个组件模拟出 CSS 作用域；
1.     支持开发期组件的热重载。
```
// 安装包
npm install --save-dev babel-core babel-loader vue-loader babel-preset-env babel-preset-react
```

```
// webpack.config.js
module.exports = {
    devtool: 'eval-source-map',
    entry:  __dirname + "/app/main.js",
    output: {
        path: __dirname + "/public",
        filename: "bundle.js"
    },
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: [
                            "env", "react"
                        ]
                    }
                },
                exclude: /node_modules/
            },
            {
                test: /\.vue$/,
                loader: 'vue-loader',
                options: vueLoaderConfig
            }
        ]
    }, 
    devServer: {
        contentBase: "./public", // 本地服务器所加载的页面所在的目录
        historyApiFallback: true,// 不跳转
        inline: true // 实时刷新
    }
}
```

 现在你的webpack的配置已经允许你使用ES6以及JSX的语法了
 > JSX是基于Javascript的语言扩展， 它允许在Javascript代码中插入XML语法风格的代码
 
 > 我们项目使用的是vue，vue中的jsx写法就是vue提供的render()函数)
 
 ```
 // js
 new Vue({
  el: '#app',
  data: {
    msg: 'Click to see the message'
  },
  methods: {
    hello () {
      alert('This is the message')
    }
  }
});

// html
<div id="app">
    <span 
        class="my-class" 
        style="cursor: pointer" 
        v-on:click="hello"
    >
        {{ msg }}
    </span>
</div>
 ```
 对比JSX实现
```
// js
new Vue({
  el: '#app',
  data: {
    msg: 'Click to see the message.'
  },
  methods: {
    hello () {
      alert('This is the message.')
    }
  },
  render: function render(h) {
    return (
      <span
        class={{ 'my-class': true }}
        style={{ cursor: 'pointer' }}
        on-click={ this.hello }
      >
        { this.msg }
      </span>
    )
  }
});

// index.html
<div id="app"><!--span will render here--></div>

```
#### 郊区（babel-plugin-transform-vue-jsx）
JSX是需要编译为JavaScript才可以运行的， 所以第三个样例需要有额外的编译步骤。这里我们用Babel和Webpack来进行编译。
```
// webpack.config.js
 loaders: [
  { test: /\.js$/, loader: 'babel', exclude: /node_modules/ }
]
```
或者修改你的.babelrc文件，加入 babel-plugin-transform-vue-jsx 这个插件
```
{
  "presets": ["es2015"],
  "plugins": ["transform-vue-jsx"]
}
```
 
#### 5.CSS模块

webpack提供两个工具处理样式表：css-loader 和 style-loader
1. css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能
1. style-loader将所有的计算后的样式加入页面中

二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。

```
// 安装
npm install --save-dev style-loader css-loader
```
```
//使用
module.exports = {
   ...
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            },
            {
                test: /\.vue$/,
                loader: 'vue-loader',
                options: vueLoaderConfig
            },
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "style-loader"
                    }, {
                        loader: "css-loader",
                        options: {
                            modules: true, // 指定启用css modules
                            localIdentName: '[name]-[hash:base64:5]' // 指定css的类名格式
                        }
                    }, {
                        loader: "postcss-loader"
                    }
                ]
            }
        ]
    }
};

```
配置后，可以在js文件中使用
```
import './base.css';//使用require导入css文件

打包后会生成类名为[name]-[hash:base64:5]
```

#### 6.插件（Plugins）

> 插件（Plugins）是用来拓展Webpack功能的，它们会在整个构建过程中生效，执行相关的任务。
直接对整个构建过程其作用。

1. new HtmlWebpackPlugin()
> 这个插件的作用是依据一个简单的index.html模板，生成一个自动引用你打包后的JS文件的新index.html。这在每次生成的js文件名称不同时非常有用（比如添加了hash值）。

```
plugins: [
    new HtmlWebpackPlugin({
        template: "index.ejs" // 将ejs作为模板，将打包的后的js
    })
],
```
2. new webpack.HotModuleReplacementPlugin()//热加载插件

3. 优化插件
    1. UglifyJsPlugin：压缩JS代码；
    1. ExtractTextPlugin：分离CSS和JS文件
    

#### 7. 输出
```
output: {
    path: resolve("build"), // 输出目录
    filename: "bundle-[hash].js" // 输出文件名
},
```


## 最终配置文件
```
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

function resolve(dir) {
  return path.join(__dirname, '..', dir)
}

module.exports = {
    entry: resolve("app/main.js"),
    output: {
        path: resolve("dist"),
        filename: "bundle-[hash].js"
    },
    devtool: 'none',
    devServer: {
        contentBase: "./dist", //本地服务器所加载的页面所在的目录
        historyApiFallback: true, //不跳转
        inline: true,
        hot: true
    },
    module: {
        rules: [{
            test: /(\.jsx|\.js)$/,
            use: {
                loader: "babel-loader"
            },
            exclude: /node_modules/
        }, {
            test: /\.vue$/,
            loader: 'vue-loader',
            options: vueLoaderConfig
        }, {
            test: /\.css$/,
            use: ExtractTextPlugin.extract({
                fallback: "style-loader",
                use: [{
                    loader: "css-loader",
                    options: {
                        modules: true,
                        localIdentName: '[name]__[local]--[hash:base64:5]'
                    }
                }, {
                    loader: "postcss-loader"
                }],
            })
        }]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: 'index.ejs'
        }),
        new webpack.optimize.OccurrenceOrderPlugin(),
        new webpack.optimize.UglifyJsPlugin(),
        new ExtractTextPlugin("style.css")
    ]
};
```


---
##### 参考文献
1. https://zhuanlan.zhihu.com/p/21318102?refer=leanreact
 // 含有解释
1. https://www.cnblogs.com/xuepei/p/7992423.html
 // webpack
1. https://segmentfault.com/a/1190000007972133
1. https://segmentfault.com/a/1190000012472099