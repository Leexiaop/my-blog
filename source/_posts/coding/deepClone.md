---
title: 手写JS深拷贝方法
date: 2020-11-6 10:59:18
categories: coding
tags: coding
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604641788229&di=e3acbb22a14453aa6da1ead396d487e2&imgtype=0&src=http%3A%2F%2Fa.hiphotos.baidu.com%2Fzhidao%2Fpic%2Fitem%2Fdbb44aed2e738bd42bc4fe4da18b87d6267ff9e1.jpg
---
JS的深拷贝需要考虑的点：
+ 要拷贝的数据类型：基本类型，null, 对象
+ 要不要拷贝原型链上的属性
+ 防栈溢出溢出的问题