---
title: '设计模式-设计模式（常用）'
date: '2018-05-07T20:54:11+08:00'
publishdate: '2018-05-07T20:54:11+08:00'
lastmod: '2018-05-07T20:54:11+08:00'
draft: false
tags: ['js', 'Design patterns']
series: ['js']
categories: ['Design patterns']
img: 'images/blog/HongKong/img2.jpg'
toc: true
summary: '设计模式，这是一种模板'
---

## 设计模式-设计模式

> 这是一种模板

-   三大类型（23 种模式）：

-   创建型

    -   工厂模式
    -   单列模式
    -   原型模式

-   组合型
    -   适配器模式
    -   修饰器模式
    -   代理模式
    -   外观模式
    -   桥接模式
    -   组合模式
    -   享元模式

*   行为型
    -   策略模式
    -   模板方法模式
    -   观察者模式（重）
    -   迭代器模式（重）
    -   职责连模式
    -   命令模式
    -   备忘录模式
    -   状态模式（重）
    -   访问者模式
    -   中介则模式
    -   解释器模式

#### 1. 工厂模式：

-   将 new 操作单独封装
-   构造器的名字不能乱改=。=

例 1：
买东西，你传入钱，东西名 ==> 最后就返东西。至于中间的东西是哪里来的，怎么来的你就不用担心了

```bash
class Product {
    constructor(name) {
        this.name = name
    }
    init(){
        console.log(`init ${name}`)
    }
    fn1() {
        console.log('fn1')
    }
    fn2() {
        console.log('fn2')
    }
}
class Creator {
    create(name) {
        return new Product(name)
    }
}
// 测试
let creator = new Creator() // 生成一个工厂
let p = creator.create('pp')
p.init() // init pp
p.fn1() // fn1
```

例 2：
jq

```
$('div') 其实就是 new $('div')的一次工厂模式的封装
```

例 3：React.createElement

```
class Vnode(tag, attrs, children) {
    // ...
}
React.createElement = function (tag, attrs, children) {
    return new Vnode(tag, attrs, children)
}
```

例 4：Vue 异步组件

```
Vue.component('async-comp', function(resolve, reject) {
    setTimeout(() => {
        resolve({
            template: '<div>hello, this is async</div>'
        })
    }, 1000)
})
```

#### 2. 单列模式:

-   【单一职责原则】只实例化唯一的一个对象；（弱类型语言无法完全实现 private；所以只能用闭包的形式实现一个内部变量）
-   没有违反开放封闭原则

例 1：

```bash
class SingleObj {
    login() {
        console.log('login')
    }
}
# 静态方法（挂载在Class上，不管new多少次SingleObj，也只有一个getInstance方法）
SingleObj.getInstance = (() => {
    let instance
    return function () {
        if(!instance) {
            new SingleObj()
        } else {
            return instance
        }
    }
})()
// 测试
let obj1 = SingleObj.getInstance()
obj1.login()
let obj2 = SingleObj.getInstance()
obj2.login()
console.log('测试是否单例模式：', obj1===obj2)

// 无法强制用户不去new SingleObj(), 测试
let obj3 = new SingleObj() // 直接new,当然也是有login方法的，但是是内存中的另一个变量obj3
obj3.login()
console.log('测试是否单例模式：', obj1===obj3)
```

例 2：
插件使用

```bash
if (window.lodash !== null) {
    return window.lodash
} else {
    // 初始化lodash
}
```

例 3：vuex 和 redux 中的 store

#### 观察者模式:

-   主题与观察者分离，不是主动触发，而是被动监听，两者解耦

介绍：

1. 发布和订阅
2. 一对 n

生活场景： 订阅报纸，等待送报员。

```bash
// 大环境，保存状态，状态变化之后触发所以观察者对象更新状态
class Subject {
    constructor() {
        this.state = 0
        this.observers = [] // 所以观察者
    }
    getState() {
        return this.state
    }
    setState(state) {
        this.state = state
        // 更新状态时通知所有观察者
        this.notifyAllObservers()
    }
    notifyAllObservers() {
        this.observers.forEach(observer => {
            // 每个观察者更新
            observer.update()
        })
    },
    attach(observer) {
        this.observers.push(observer)
    }
}

// 观察者
class Observer {
    constructor(name, subject) {
        this.name = name
        this.subject = subject
        # 大环境中添加当前的观察者
        this.subject.attach(this)
    },
    update() {
        console.log(`${this.name} update, state: ${this.subject.getState}`)
    }
}

// 测试
let s = new Subject()
let ob1 = new Observer('ob1', s)
let ob2 = new Observer('ob2', s)
let ob3 = new Observer('ob3', s)

s.setState(1)
// ob1 update, state: 1
// ob2 update, state: 1
// ob3 update, state: 1
s.setState(2)
// ob1 update, state: 2
// ob2 update, state: 2
// ob3 update, state: 2
```

场景：

-   网页时间绑定;（事件监听，如：给 dom 添加点击事件。点击才会触发）
-   promise;（.then(function () => {})其实的函数需要等待 promise 的状态变化，才执行 resolve 或者 reject）
-   callbacks;
-   nodejs 自定义事件，处理 http 请求；多进程通讯
-   Vue 或 React 组件生命周期触发
-   Vue watch 等等...

```bash
// nodejs 自定义事件
const EvnetEmitter = require(events).EventEmitter
const emitter1 = new EvnetEmitter()
emitter1.on('fn1', info => {
    console.log('fn1', info)
})
emitter1.on('fn1', info => {
    console.log('fn2', info)
})

emitter1.emit('fn1', 'xxx')
```

#### 迭代器模式:

-   顺序访问一个集合（有序的列表）
-   使用者无需了解集合中的结构，长度（封装）

1. 迭代器对象与目标对象分离
2. 迭代器将使用者与目标对象合理开
3. 符合开放封闭原则

场景： 获取 li 列表

```bash
html:
<ul>
    <li class="li">文本111</li>
    <li class="li">文本222</li>
    <li class="li">文本333</li>
</ul>

js:
var arr = [1,2,3]
var liList = document.getElementByTagName('li')

function each(data) {
    var $data = $(data) // 使用jq，生成迭代器
    $data.each(function(key, val) {
        console.log(key, val)
    })
}
each(arr)
each(liList)
```

迭代器：

```bash
class Container {
    constructor() {
        this.list = list
    }
    // 生成遍历器
    getIterator() {
        return new Interator(this)
    }
}

class Interator {
    constructor(container) {
        this.list = container.list
        this.index = 0
    }
    next() {
        if(this.hasNext()) {
            return this.list[this.index++]
        }
        return null
    }
    hasNext() {
        if(this.index = this.list.length) {
            return false
        }
        return true
    }
}

var arr = [1,2,3,4,5]
let container = new Container(arr)
let interator = container.getIterator()
while(interator.hasNext()) {
    console.log(interator.next())
}
```

es6 中的迭代器：

```bash
function each(data) {
    // 生成遍历器
    let iterator = data[Symbol.iterator()]

    let item = {done: false}
    while(!item.done) {
        item = iterator.next()
        if(!item.done) {
            console.log(item.done)
        }
    }
}
===
for of就是迭代器。只能对含有data[Symbol.iterator]的集合使用
```

