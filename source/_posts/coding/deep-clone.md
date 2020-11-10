---
title: 手写JS深拷贝方法
date: 2020-11-6 10:59:18
categories: coding
tags: coding
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604641788229&di=e3acbb22a14453aa6da1ead396d487e2&imgtype=0&src=http%3A%2F%2Fa.hiphotos.baidu.com%2Fzhidao%2Fpic%2Fitem%2Fdbb44aed2e738bd42bc4fe4da18b87d6267ff9e1.jpg
---
## JS的深拷贝需要考虑的点：
+ 要拷贝的数据类型：基本类型，null, 对象
+ 要不要拷贝原型链上的属性
+ 支持深层级，且防止循环引用造成防栈溢出溢出的问题

## 解析
+ 判断数据类型方法很多，但是对于我们来说，typeof就足够了，因为我们只需要区分是基本类型，还是对象，或者是null(typeof检测null也是一个对象)这三种，如果是基本类型或者null,那么直接做赋值运算，赋值即为拷贝。
```
let deepClone = (item) {
    let result
    if (typeof item !== 'Object' || item === null) {
        result = item
    }
    return result
}
```
这是对基本类型数据或者是null的处理。
+ 如果不是基本类型，也不是null,那么就是对象或者是数组这样的引用类型。这需要我们考虑他们的深拷贝，以及是不是要拷贝原型链上的属性。需要通过obj.hasOwnPropty(),
```
if (item.hasOwnPropty(key)) {
    result[key] = deepClone(item[key])
}
```
+ 循环引用，造成栈溢出的问题。那么什么是循环引用呢？且看下面的代码：
```
const obj = {}
obj.item = obj
const result = deepClont(obj)
console.log(result)
```
注意前俩行代码，这就会造成循环引用，造成栈溢出问题，那么解决这个问题的核心就是去检查遍历的每一个对象和数组是不是在之前出现过，我们可以用Map结构来实现数据的缓存。
```
let deepClone = (obj) => {
    let map = new Map()
    if (map.has(obj)) {
        return map.get(obj)
    }
    let result = Array.isArray(obj) ? [] : {}
    map.set(obj, result)
    for(let key in obj) {
        result[key] = deepClone(obj[key])
    }
    return result
}
```
下面我们来看完整的深拷贝代码：
```
let deepClone = (item) => {
    if (typeof item !== 'Object' || item === null) {
        return item
    }
    const map = new Map()
    if (map.has(item)) {
        return map.get(item)
    }
    let result = Array.isArray(item) ? [] : {}
    map.set(item, result)
    for(let key in item) {
        if (item.hasOwnPropty(key)) {
            result[key] = deepClone(item[key])
        }
    }
    return result
}
```

## 补充知识
#### Map结构
Map数据结构类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。
```
    const map = new Map();
    
    const k1 = ['a'];
    const k2 = ['a'];
    
    map
    .set(k1, 111)
    .set(k2, 222);
    
    map.get(k1) // 111
    map.get(k2) // 222
```
Map结构的实例属性：
（1）size属性，总是返回Map结构的成员总数。
```
    const map = new Map();
    map.set('foo', true);
    map.set('bar', false);
    map.size // 2
```
（2）set(key, value)
set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。
```
    const m = new Map();
    
    m.set('edition', 6)        // 键是字符串
    m.set(262, 'standard')     // 键是数值
    m.set(undefined, 'nah')    // 键是 undefined
```
set方法返回的是当前的Map对象，因此可以采用链式写法。
```
    let map = new Map()
      .set(1, 'a')
      .set(2, 'b')
      .set(3, 'c');
      返回一个Map结构的集合
    //size: 3
    //__proto__: Map
    // [[Entries]]: Array(3)
    // 0: {1 => "a"}
    // 1: {2 => "b"}
    // 2: {3 => "c"}
    // length: 3
```
（4）has(key)
has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。
```
    const m = new Map();
    
    m.set('edition', 6);
    m.set(262, 'standard');
    m.set(undefined, 'nah');
    
    m.has('edition')     // true
    m.has('years')       // false
    m.has(262)           // true
    m.has(undefined)     // true
```
（5）delete(key)
delete方法删除某个键，返回true。如果删除失败，返回false。
（6）clear()
clear方法清除所有成员，没有返回值。
```
    let map = new Map();
    map.set('foo', true);
    map.set('bar', false);
    
    map.size // 2
    map.clear()
    map.size // 0
```
