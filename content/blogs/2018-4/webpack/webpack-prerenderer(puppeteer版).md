---
title: 'webpack-prerenderer'
date: '2017-10-25T20:54:11+08:00'
publishdate: '2017-10-25T20:54:11+08:00'
lastmod: '2017-10-25T20:54:11+08:00'
draft: false
tags: ['webpack', 'web', 'puppeteer']
series: ['webpack']
categories: ['prerenderer']
# img: "images/banner/test1.jpg"
toc: true
summary: 'This is an example'
---

## 预渲染


* 1.run build生成打包html和资源上传cdn
* 2.利用生成的html 启动本地服务
* 3.使用puppeteer爬去启动的服务页面的html
* 4.生成预渲染后的html

命令行：
```
"build-pre": "npm run clean && node build/build.js --pre --e", // --pre为Boolean，设置了则启动预渲染模式
```

1. build.js
```
console.log(chalk.magentaBright('  Build Complete.And coding in ./dist \n '))

    // 预渲染
    const prerenderPromise = await prerender()
    if (isPre && prerenderPromise) {
        prerenderPromise
            .then(() => {
                spinner.stop()
                console.log(chalk.cyan('  Prerender complete.\n'))
            })
            .catch((e) => {
                console.log(chalk.red(`  Prerender failed with errors.\n${e}`))
                process.exit(1)
            })
    }

```

2. prerender.js
```
/*
*  config/index.js文件中配置了assetsPublicPath
*  将这个路径改成了cdn路径,而由于构建的js文件还没上传到cdn上,js文件会访问不到,自然无法根据js就生成html内容
 */
const fse = require('fs-extra')
const chalk = require('chalk')
const path = require('path')
const Promise = require('bluebird')
const puppeteer = require('puppeteer') // 爬取html
const express = require('express')
const config = require('../config')

// 用来预渲染的本地服务器端口
const RENDER_SERVER_PORT = 9999

/**
 * 对需要预渲染的入口进行预渲染
 * @return {Promise|null} 是否已进入预渲染流程
 */
function prerender() {
    if (config.build.env.QN_BUCKET === 'spacdn') {
        console.log('spacdn无法从本地进行访问，跳过预渲染环节')
        return null
    }
    // 取出需要预渲染的入口列表
    const entryInfoList = config.build.entryInfoList.filter(item => item.prerenderPathList)
    console.log('pre/entryInfoList', entryInfoList)
    if (!entryInfoList.length) {
        return null
    }

    console.log(chalk.magenta('开始预渲染'))
    const start = Date.now()
    return Promise.map(entryInfoList, (item) => {
        return prerenderFile(config.build.assetsRoot, `${item.entryName}.html`, item.prerenderPathList)
    }).then(() => {
        console.log(chalk.magenta(`预渲染全部完成，耗时${Date.now() - start}ms`))
    })
}

/**
 * 基于build结果，重新进行一遍预渲染
 * @param {string} assetsRoot 静态资源根目录
 * @param {string} htmlFileName 文件名
 * @param {[string]} prerenderPathList 要渲染的页面相对路径列表
 * @return {Promise}
 */
async function prerenderFile(assetsRoot, htmlFileName, prerenderPathList) {
    // 存一份原文件xx.html==>xx_orig.html
    const srcFileName = backupPrerenderFile(assetsRoot, htmlFileName)
    // 启动本地服务到xx_orig.html
    const server = await startServer(assetsRoot, srcFileName, RENDER_SERVER_PORT)

    // 遍历路由列表（routePath为路由入口）
    await Promise.map(prerenderPathList, async (routePath) => {
        console.log(`开始预渲染: ${srcFileName}, routePath: ${routePath}`)

        const prerenderedHTML = await getHtml(RENDER_SERVER_PORT, routePath)

        // console.log('预渲染prerenderedHTML:', prerenderedHTML)
        const dstFilePath = path.join(assetsRoot, routePath, htmlFileName)
        try {
            await fse.writeFile(dstFilePath, prerenderedHTML)
        } catch (error) {
            if (error) {
                console.log(`Could not write file: ${dstFilePath}`)
                throw error
            }
        }
        console.log(` 预渲染: ${srcFileName}\n 路由: ${routePath} 完成，\n 生成: ${htmlFileName}`)
    })
    server.close()
}

/**
 * 备份预渲染前的文件
 * @param {string} assetsRoot 文件目录
 * @param {string} htmlFileName 文件名
 */
function backupPrerenderFile(assetsRoot, htmlFileName) {
    const src = path.join(assetsRoot, htmlFileName)
    // 备份文件名（_orig.html）
    const dstFileName = htmlFileName.replace('.html', '_orig.html')
    const dst = path.join(assetsRoot, dstFileName)
    if (!fse.pathExistsSync(dst)) {
        console.log('生成_orig.html', dst)
        fse.copySync(src, dst)
    }
    return dstFileName
}

function startServer(assetsRoot, indexFileName, port) {
    const app = express()

    // 设置静态资源目录
    app.use(express.static(assetsRoot))
    // 首页
    app.get('/', (req, res) => {
        res.redirect(`/${indexFileName}`)
    })
    // 404
    app.use((req, res) => res.sendStatus(404))

    return new Promise((resolve, reject) => {
        const server = app.listen(port, (error) => {
            if (error) {
                reject(error)
            } else {
                console.log(chalk.green(`prerender server listening on:${port}`))
                resolve(server)
            }
        })
    })
}
/**
 * @param {string} url 爬取页面url
 * @param {number} port
 * @param {number} routePath 路由'/', 'more'
 */
function getHtml(port, routePath) {
    const openUrl = `http://localhost:${port}${routePath}`

    return (async () => {
        const browser = await puppeteer.launch()
        const page = await browser.newPage()
        await page.goto(openUrl)
        console.log(`go to ${openUrl}`)

        const bodyHandle = await page.$('html')
        const html = await page.evaluate(body => body.innerHTML, bodyHandle)
        const htmlPage = `<!DOCTYPE html><html lang=en>${html}</html>`
        browser.close()
        return htmlPage
    })()
}

module.exports = prerender

```