---
title: "Typescript"
date: 2019-05-03T17:17:53+08:00
publishdate: 2019-05-03T17:17:53+08:00
lastmod: 2019-05-03T17:17:53+08:00
draft: false
img: "images/blog/img.jpg"
toc: true
summary: "Typescript从入门到了解"
---


### 搭建运行ts的环境

> 需要安装 node.js typescript

```
// 查看node版本
node -v 
npm i -g typescript
npm i -g ts-node

// 初始化 tsconfig.js
tsc -init
// 编译生成 .js 文件
tsc demo.ts 
```

### ts的优势

- 静态类型抛错明显，动态语言（js）运行才会抛错
- 更好的代码提示，类型、属性、方法都是interface/type定义好的

---

### 1. 类型

#### 基础类型

1. string 
1. number 
1. boolean 
1. null 
1. undefined 
1. symbol
1. void

```
let a: string = 'cl'
let b: number = 18
let isDev: boolean = true
```

#### 其他类型

1. 函数类型
1. 对象类型
1. 类类型

```
const getNumber: () => number = () => {
  return 123
}
const teacher: {name: string; age: number} = {
  name: 'ss',
  age: 18
}
class Person {}
const clss: Person = new Person()
```

#### type annotation 类型注解 

> ts告诉我们变量是什么类型

#### type inference 类型推断 

> ts会自动分析变量类型

```
let firstNumber = 1
let secondNumber = 1
let total = firstNumber + secondNumber

// 但是 参数在未调用的时候是不知道具体类型的时候需要类型注解
function getTotal(firstNumber: number, secondNumber: number) {
  return firstNumber + secondNumber
}
let total1 = getTotal(1, 2)

let obj = {
  name: 'cl',
  age: 18
}
const date = new Date() // 可识别

// ts无法识别JSON.parse的返回类型
interface Person ={
    name: string
}
const rawData: Person = JSON.parse('{"name": "cl"}')
```

### 2. 函数返回值

- (): void => {}
- (): number => {}
- (): never => {
    while(true){}
}

```
// 解构赋值
function add({first, second}: {first: number; second: number}): number {
  return first + second
}

add({first: 1, second: 2})
```

### 3. 数组 元祖

数组
```
// 可类型推断
let numArr: (number)[] = [1,2,3]
let strArr: (string)[] = ['a','b','c']
let Arr: (number|string)[] = [1,2,3]
let undefArr: undefined[] = [undefined]

// 对象数组类型，类型太长。
let objArr: {name: string, age: number}[] = [{
    name: 'cl',
    age: 18
}]
// 类型别名处理
type User = {name: string, age: number}
let objArr: User[] = [{
    name: 'cl',
    age: 18
}]
```
元祖tuple (约束数组的长度及每项的类型) 
```
const teacherInfo: [string,string,number] = ['cl', 'male', 19]
// csv
``` 

### 4. interface 

> 接口只是帮助校验类型，并不会被编译成js。而是被剔除

type 与 interface的区别
```
// type 可以代表string 等基础类型
type Person = string

// interface 只能代表函数或对象
interface Person = {
    name: string
    age?: number // 可有可无
    readonly ID: string // 只读类型
    [propName: string]: any // 传递多余的字符串也不会报错了
    say(): void
}

const p1 = {
    name: 'cl',
    sex: 'm'
}

const setPerson({person: Person}):void {
    console.log(person)
}

// 直接直面量调用会报错（强校验）
setPerson({
    name: 'ss',
    sex: 'f',
    say() {}
})
// 传递变量则不会报错
setPerson(p1) 

class User implements Person {
    // 应用Person接口到User上，所以这里必须有name ID say()
    name = 'cl'
    ID = '123'
    say() {}
}

interface Teacher extends Person {
    // 老师继承自人
    teach() {}
}
```

### 类 

#### class访问类型

- pubilic 运行在类内（this.name）或类外调用（p1.name）
- private 运行在类内部调用（this.name）
- protected 运行在类内及继承的子类中调用

```
// 方式1：
class Person {
    name: string
    constructor(name: string) {
        this.name = name
    }
}
// 方式2：
class Person {
    constructor(public name: string) {}
}

const p1 = new Person('cl')

```
#### super() 的作用

> 子类中执行父类构造器，实现继承

```
class Person {
    constructor(public name: string) {}
}

class Teacher extends Person {
    constructor(public age:number) {
        super('xxx')
    }
}
const t1 = new Teacher(18)
```

#### getter setter 是属性

```
class Person {
    constructor(private _name: string) {}
    // 暴露方法可以访问name
    get name() {
        return `${this._name} ?!`
    }
    set name() {
        const realName = name.split(' ')[0]
        this._name = realName
    }
}

const p = new Person('cl')
p.name // cl ?!

```

---

## 进阶

#### 1. 联合类型

> animal: Bird | Dog

#### 2. 类型保护

- as 语法 类型断言
- in 语法 类型断言
- typeof 语法做类型保护
- instanceof 语法做语法保护 【仅class可用】

```
interface Bird{
    eat: void,
    fly: () => {}
}
interface Dog{
    eat: void,
    bark: () => {}
}


function trainAnial(animal: Bird|Dog) {
    // 方式1：as
    if(animal.fly) {
        (animal as Bird).fly()
    } else {
        // 类型断言
        (animal as Dog).bark()
    }
    // 方式2：in
    if('fly' in animal) {
        animal.fly()
    } else {
        animal.bark()
    }
}

```

#### enum 枚举类型

```
// js对象模拟enum
const Status = {
    OFF_LINE: 0,
    ON_LINE: 1,
    DELETE: 2,
}

// 枚举
enum Status {
    OFF_LINE,       // 默认是0
    ON_LINE = 3,    // 3
    DELETE,         // 4
}
```

### 泛型 generic

> 泛指的类型

#### 1. 函数泛型
```
function add<T,P>(params1: T,params2: P) : T{
    return `${params1} ${params2}`
}

// T就是string类型，P就是number类型
add<string, number>('1',2) // 显示的告诉类型
add('1',2) // 隐式的类型推断

```

#### 2. 类的泛型

```
interface Item {
    name: string
}
// 泛型T拥有Item中的所有属性
class Person<T extends Item> {
    constructor(private data:T[]) {}
    getItem(id: number): string {
        return this.data[id].name
    }
}

const p1 = new Person([{
    name: 'cl'
}])
```

```
interface Item {
    name: string
}
// 泛型T只能是number或者string
class Person<T extends number | string> {
    constructor(private data:T[]) {}
    getItem(id: number): T {
        return this.data[id]
    }
}

const p1 = new Person([])
```

#### 3. 泛型还可以作为类型注解

```
const fn: <T>() => string = <T>() => {
    return 'string'
}
```

### 命名空间 namespace

```
namespace Home {
    class Header{
        constructor() {
          const elem = document.createElement('div')
          elem.innerText = 'This is Herder'
          document.body.appendChild(elem)
        }
    }
    ...
    class Footer{}
    
    // 导出全局使用的
    export class Page{
        new Header()
        new Footer()
    }
}
```
相互引用的ts 声明依赖
```
// index.ts 依赖于（使用） xx.ts 
///<reference path='./xx.ts'>

```

### es6的import 比 namespace 更直观的看到导入的模块名


### 类型定义文件 .d.ts

> 类型定义帮助 ts 理解 js

```
// jq.d.ts
// 定义全局变量
declare var $: (param:() => void ) => void

// 定义全局函数
declare function $(params: string): {
    html: (html:string) => {}
}


// 使用 interface 语法实现函数重载
interface JqueryInstance {
    html: (html:string) => JqueryInstance 
}

interface JQuery {
    (readFunc: () => void): void;
    (select: string): JqueryInstance;
}

// 对对象进行类型定义 以及对类进行定义 以及命名空间的定义
declare namespace $ {
    namespace fn {
        class init {}
    }
}
```
```
// ES6 模块化混合类型定义
declare module 'jquery' {
    interface JqueryInstance {
        html: (html:string) => JqueryInstance 
    }
    function $(readFunc: () => void): void;
    function $(select: string): JqueryInstance;
    namespace $ {
        namespace fn {
            class init {}
        }
    }
    export = $
}
    

```

### keyof

```
getInfo<T extends keyof Person>(key: T) :Person[T] {
    return this.info[key]
}
```


### 类的装饰器(函数)

> 声明类的时候就会执行一次装饰器

```
tsconfig.json
experimentalDecorators: true
emitDecoratorMetadata: true
```

```
// 类装饰器接收的参数是构造器
function testDecorator(constructor: any) {}

@testDecorator
class Person{}

const p1 = new Person()
```






<br><br>
