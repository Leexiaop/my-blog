---
title: Recoil基础--基础介绍
date: 2020-11-2 18:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604296909918&di=04e7bdeca6fa5ca734f5897319c876fa&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2Fb%2F53c749c82fdcd.jpg
---
# Intro
This section assumes you have installed Recoil and React. See the [Getting Started](https://recoiljs.org/docs/introduction/getting-started) page for how to get started with Recoil and React from scratch. Components in the following sections are assumed to have a `<RecoilRoot />` in the parent tree.
这一章节主要是安装Recoil和React,可以查看[开始](https://recoiljs.org/docs/introduction/getting-started)页如何安装Recoil和React,在下面的章节中，根组件中都会使用`<RecoilRoot />`组件。
In this tutorial, we'll be building a simple todo-list application. Our app will be able to do the following:
我们会创建一个简单的todo-list的应用。我们的应用会做到以下几点：
+ Add todo items 添加todo项
+ Edit todo items 开发todo项
+ Delete todo items 删除todo项
+ Filter todo items 过滤todo项
+ Display useful stats 展示state
Along the way, we'll cover atoms, selectors, atom families, and the hooks exposed by the Recoil API. We'll also cover optimization
这样，我们会很好的覆盖到Atoms,selectors,atom全家桶，以及Recoil API 暴露的hooks。