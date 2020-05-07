---
title: '编程思想-函数式编程'
date: '2018-07-15T20:42:11+08:00'
publishdate: '2018-07-15T20:42:11+08:00'
lastmod: '2018-07-15T20:42:11+08:00'
draft: false
tags: ['编程思想', '函数式编程']
series: ['编程思想']
categories: ['编程思想']
toc: true
summary: '编程思想-函数式编程'
---

## 编程的思想

> 函数式编程,所属于声明式编程

> 面向对象编程(OOP)通过封装变化使得代码更易理解。 函数式编程(FP)通过最小化变化使得代码更易理解。 -- Michacel Feathers（Twitter）

设计原则：

1. 可扩展性--添加新功能是否需要修改以前的代码？
2. 易模块化--修改了一个模块，另一个模块是否会受到影响？
3. 可重用性--是否有很多重复的代码？
4. 可测性--给函数添加单元测试是否让你难受？
5. 易推理性--代码是否非结构化严重并难以推理？

#### 什么是函数式编程？

> 函数式编程是种编程方式，它将电脑运算视为函数的计算。函数编程语言最重要的基础是 λ 演算（lambda calculus），而且 λ 演算的函数可以接受函数当作输入（参数）和输出（返回值）。

嗯..百度就是这么说的。那么你能懂吗？不能就看下面

> "函数式编程"是一种"编程范式"（programming paradigm），也就是如何编写程序的方法论。是一种强调以函数使用为主的软件开发风格
> 再举个栗子说，在页面显示一句话'你好'

```bash
# 一般情况
document.querySelector('#div1').innerHtml = '你好'

```

```bash
# 封装一下
function printMessage(eleId, msg) {
    document.querySelector(eleId).innerHTML = `${msg}`
}
```

```bash
# 函数式开发者
const printMessage = compose(addToDom('msg', echo))  // addToDom，echo都是函数
printMessage('你好')
// compose 简单解释一下，他会让函数从最后一个参数顺序执行到第一个参数，compose 的每个参数都是函数。
这样的方式是不是很像组件化，里面的函数就是一个一个的小组件。组合在一起就实现了最终的效果

```

> 函数式编程属于声明是编程范式：这种范式会描述一系列的操作，但并不会暴露它们是如何实现的或是数据流如何传过它们。

#### compose 补充：

1. compose 的参数是函数，返回的也是一个函数。
2. 因为除了第一个函数的接受参数，其他函数的接受参数都是上一个函数的返回值，所以初始函数的参数是多元的（本题只讨论了一元参数的情况），而其他函数的接受值是一元的。
3. compsoe 函数可以接受任意的参数，所有的参数都是函数，且执行方向是自右向左的，初始函数一定放到参数的最右面。

lodash.js 实现方式：

```bash
var flow = function(funcs) {
    var length = funcs.length
    var index = length
    while (index--) {
        if (typeof funcs[index] !== 'function') {
            throw new TypeError('Expected a function');
        }
    }
    return function(...args) {
        var index = 0
        var result = length ? funcs[index].apply(this, args) : args[0]
        while (++index < length) {
            result = funcs[index].call(this, result)
        }
        return result
    }
}
var flowRight = function(funcs) {
    return flow(funcs.reverse())
}
```

#### 对比命令式编程和声明式编程：

```bash
// 命令式方式(命令式很具体的告诉计算机如何执行某个任务。)
var array = [0, 1, 2, 3]
for(let i = 0; i < array.length; i++) {
    array[i] = Math.pow(array[i], 2)
}
array; // [0, 1, 4, 9]

// 声明式方式(将程序的描述与求值分离开来。它关注如何用各种表达式来描述程序逻辑，而不一定要指明其控制流或状态关系的变化。)
[0, 1, 2, 3].map(num => Math.pow(num, 2))
```

为什么我们要去掉代码循环呢？
循环是一种重要的命令控制结构，但很难重用，并且很难插入其他操作中。而函数式编程旨在尽可能的提高代码的无状态性和不变性。要做到这一点，就要学会使用无副作用的函数--也称纯函数

#### 纯函数

> 纯函数指没有副作用的函数。所谓"副作用"（side effect），指的是函数内部与外部互动（最典型的情况，就是修改全局变量的值），产生运算以外的其他结果。
> 函数式编程强调没有"副作用"，意味着函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。

```bash
// 不纯
var count = 0
function increment() {
    return ++count;
}
# 这个函数就是不纯的，它读取了外部的变量，可能会觉得这段代码没有什么问题，但是我们要知道这种依赖外部变量来进行的计算，计算结果很难预测，你也有可能在其他地方修改了 counter 的值，导致你 increment 出来的值不是你预期的。

// 纯函数
function increment(num) {
    return ++num;
}
纯函数特点：
1. 仅取决于提供的输入，而不依赖于任何在函数求值或调用间隔时可能变化的隐藏状态和外部状态。
2. 不会造成超出作用域的变化，例如修改全局变量或引用传递的参数。
```

场景例子：
根据用户 id 查询用户，并显示在页面上

```bash
// 命令式代码

function showUser(id) {
    // 这里假如是同步查询
    const user = axios.get(id)
    if(user !== null) {
          // 读取外部的 eleId
          document.querySelector(`${eleId}`).innerHTML = `${user.id},${user.name}`
    } else {
        throw new Error('not found')
    }
}
showUser(10) // 10, xxx
```

```bash
// 函数式编程

function findUser(id) {
    // 这里假如是同步查询
    const user = axios.get(id)
    if(user !== null) {
        return user
    } else {
        throw new Error('not found')
    }
}
// 将用户format
function format(user) => `${user.id},${user.name}`
// 显示用户信息
function showUser(eleId, info) {
    document.querySelector(eleId).innerHTML = info
})
const showStudent = compose(showUser('#user'), format, findUser(id))
showStudent(10)
```

#### 优点

1. 代码简洁，开发快速
2. 接近自然语言，易于理解

```bash
    前文曾经将表达式(1 + 2) * 3 - 4，写成函数式语言：
    subtract(multiply(add(1,2), 3), 4)
    对它进行变形，不难得到另一种写法：
    add(1,2).multiply(3).subtract(4)
    这基本就是自然语言的表达了。
```

3. 灵活, 方便的代码管理。
4. 组件化思想。
5. 声明式的风格，给高阶步骤提供了一个清晰视图，增强了代码的可读性
6. 另外是将纯函数与不纯的行为分离出来。
