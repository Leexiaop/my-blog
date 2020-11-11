---
title: 手动实现Javascript中new操作符
date: 2020-11-10 16:31:19
categories: coding
tags: coding
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1605007006167&di=3cc88e24c52fb72f4c46d0a466974f9a&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2F6%2F594b222f58051.jpg
---
想要实现new操作符，首先要知道new操作符都干了什么事儿？
+ 创建了一个空，将this指向这个对象
+ 将创建的空对象的__proto__属性继承自构造函数的prototype属性，也就是说是继承构造函数的原型对象上的方法和属性
+ 调用构造函数，将构造函数中的this替换为空对象的this，继承构造函数中的属性
+ 返回函数内部的这个新对象

```
function _new (fun) {
    return function () {
        let obj = {}
        obj.__proto__ = fun.prototype
        fun.call(obj, ...arguments)
        return obj
    }
}
```
测试：
```
function Person (name, age) {
    this.name = name
    this.age = age
}
let person = _new(Person)('小明', 3) //=> {name: '小明', age: 3}
```