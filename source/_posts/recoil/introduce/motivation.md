---
title: Recoil简介--设计初衷
date: 2020-10-28 18:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1603965596355&di=4d0f1dda3d6870384b5dff3583bab278&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2Fb%2F54f3f3fb543fa.jpg
---
### Motivation 初衷
For reasons of compatibility and simplicity, it's best to use React's built-in state management capabilities rather than external global state. But React has certain limitations:
秉承简单与兼容性至上的原则，最好的状态管理方式当然是直接使用 React 内置能力而不是外部全部状态。然而 React 确实存在着一些问题：

+ Component state can only be shared by pushing it up to the common ancestor, but this might include a huge tree that then needs to re-render.
组件状态只能与其祖先组件进行共享，这可能会带来组件树中大量的重绘开销。
+ Context can only store a single value, not an indefinite set of values each with its own consumers.
Context 只能保存一个特定值而不是与其 Consumer 共享一组不确定的值。
+ Both of these make it difficult to code-split the top of the tree (where the state has to live) from the leaves of the tree (where the state is used).
以上两点导致组件树顶部组件（状态生产者）与组件树底部组件（状态消费者）之间的代码拆分变得非常困难。

We want to improve this while keeping both the API and the semantics and behavior as Reactish as possible.
我们希望在尽可能保持 React 代码风格和语义化的前提下解决以上问题。


Recoil defines a directed graph orthogonal to but also intrinsic and attached to your React tree. State changes flow from the roots of this graph (which we call atoms) through pure functions (which we call selectors) and into components. With this approach:
Recoil 在组件树中定义了一个正交且内聚的单向图谱。状态变更通过以下方法从图谱的底部（atoms）通过纯函数（selectors）进入组件：

+ We get a boilerplate-free API where shared state has the same simple get/set interface as React local state (yet can be encapsulated with reducers etc. if needed).
我们提供了一些无依赖的方法，这些方法像 React 局部状态一样暴露相同的 get/set 接口（简单理解为 reducers 之类的概念亦可）。
+ We have the possibility of compatibility with Concurrent Mode and other new React features as they become available.
我们能够与一些 React 新功能（比如并发模式）兼容。
+ The state definition is incremental and distributed, making code-splitting possible.
状态定义是可伸缩和分布式的，代码拆分成为可能。
+ State can be replaced with derived data without modifying the components that use it.
不用修改组件即可派生数据状态。
+ Derived data can move between being synchronous and asynchronous without modifying the components that use it.
派生数据状态支持同步和异步。
+ We can treat navigation as a first-class concept, even encoding state transitions in links.
我们把跳转看作一级概念，甚至可以对链接中的状态流转进行编码。
+ It's easy to persist the entire application state in a way that is backwards-compatible, so persisted states can survive application changes.
所以可以简单地使用向后兼容的方式来持久化整个应用的状态，应用变更时持久化状态也可以因此得以保留。
