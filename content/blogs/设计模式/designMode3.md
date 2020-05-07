---
title: '设计模式-设计模式（不常用）'
date: '2018-05-07T20:54:11+08:00'
publishdate: '2018-05-07T20:54:11+08:00'
lastmod: '2018-05-07T20:54:11+08:00'
draft: false
tags: ['js', 'Design patterns']
series: ['js']
categories: ['Design patterns']
img: 'images/blog/HongKong/img3.jpg'
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

#### 1. 原型模式：

- 基于原有数据复制一份
```bash
var protptype = {
    getName: function () {
        return this.name
    },
    say: function () {
        console.log('hello')
    }
}
Object.create(protptype)
```

#### 桥接模式:

-   用于把抽象化与实现化解耦
-   二者独立变化

#### 组合模式:

> 树形结构，嵌套组合

-   整体和单个节点操作是一致的
-   整体和单个节点的数据也保持一致
-   将整体和单个节点的操作抽象出来
-   符合开放封闭原则

#### 享元模式

-   共享内存（主要考虑内存，而非效率），避免内存开销
-   相同数据，共享使用

-   将数据部分抽象出来
-   符合开放封闭原则

#### 策略模式

-   不通策略，分开处理
-   避免大量 if...else, switch...case
-   开放封闭原则

例如:

```bash
// 场景,不同等级的用户
class User{
    constructor(type) {
        this.type = type
    }
    hello() {
        if(this.type === 'vip1') {
            console.log('i am vip1')
        } else if(this.type==='vip2'){
            console.log('i am vip2')
        } else if(this.type==='user'){
            console.log('i am user')
        }
    }
}
let v1 = new User('vip1')
v1.hello()
let v1 = new User('vip2')
v1.hello()
let v1 = new User('user')
v1.hello()

// 策略模式
# 不通用户等级，会有不同的初始化方式
class Vip1 {
    hello() {
        console.log('i am vip1')
    }
}
class Vip2 {
    hello() {
        console.log('i am vip2')
    }
}
class User {
    hello() {
        console.log('i am user')
    }
}
let v1 = new Vip1('vip1')
v1.hello()
let v1 = new Vip2('vip2')
v1.hello()
let v1 = new User('user')
v1.hello()
```

#### 模板方法模式和职责连模式

-   一步操作可能分为多个职责角色完成
-   把这些角色都分开，然后用一个链穿起来
-   发起者和处理者分离

```bash
class Action{
    handle() {
        fn1()
        fn2()
        fn3()
    }
    fn1(){}
    fn2(){}
    fn3(){}
}
```

#### 命令模式

- 执行命令时，发布者和执行者分开
- 中间加个命令对象，作为中转站
```bash
// 接收者
class Receiver{
    exec() {
        console.log('do ..')
    }
}
// 命令者
class Command{
    constructor(receiver) {
        this.receiver = receiver
    },
    cmd() {
        console.log('让 receiver do')
        this.reveiver.exec()
    }
}
// 发布命令的人
class Invoker {
    constructor(command){
        this.command = command
    }
    invoker() {
        console.log('开始')
        this.command.cmd()
    }
}
// 士兵
let soldier = new Receiver()
// 小号手
let trumpter = new Command(soldier)
// 将军
let general = new Invoker(trumpter)
general.invoker()

```

#### 备忘录模式

- 随时记录一个对象的状态变化
- 随时可以恢复之前的某个状态（撤销）

```bash
// 备忘类
class Memento {
    constructor(content) {
        this.content = content
    }
    getVContent() {
        return this.content
    }
}
// 备忘列表
class CareTaker {
    constructor() {
        this.list = []
    }
    add(memento) {
        this.list.push(mememto)
    }
    get(index) {
        return this.list[index]
    }
}
// 编辑器
class Editor{
    constructor() {
        this.content = null
    }
    setContent(content) {
        this.content = content
    }
    getContent(content) {
        return this.content
    }
    saveContentToMemento() {
        return new Memento(this.content)
    }
    getContentFromMemento(memento) {
        this.content = memento.getContent()
    }
}

// 测试
let editor = new Editor()
let careTaker = new CareTaker()
editor.setContent('test1')
editor.setContent('test2')
careTaker.add(editor.saveContentToMemento()) // 备份
editor.setContent('test3')
careTaker.add(editor.saveContentToMemento()) // 备份
editor.setContent('test4')
console.log(editor.getContent())
editor.getContentFromMemento(careTaker.get(1))
console.log(editor.getContent())
editor.getContentFromMemento(careTaker.get(0))

```

#### 中介者模式
```bash
class A {
    constructor() {
        this.number = 0
    }
    setNumber(num, m) {
        this.number = num
        if(m) {
            m.setB()
        }
    }
}
class B {
    constructor() {
        this.number = 0
    }
    setNumber(num, m) {
        this.number = num
        if(m) {
            m.setA()
        }
    }
}
// 中介者
class Mediator {
    construcor(a,b) {
        this.a = a
        this.b = b
    }
    setB() {
        let number = this.a.number
        this.b.setNumber(number * 100)
    }
    setA() {
        let number = this.b.number
        this.a.setNumber(number / 100)
    }
}
let a = new A()
let b = new B()
let m = new Mediator(a, b)
a.setNumber(100, m)
console.log(a.number, b.number)
b.setNumber(100, m)
```

#### 访问者模式

- 将数据操作和数据结构进行分离

#### 解释器模式

- 描述语言语法如何定义，如何解释和编译
- 用于专业场景，babel解析js等。。