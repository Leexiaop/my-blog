---
title: Recoil简介--核心概念
date: 2020-10-28 18:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1603965430181&di=6ab3a24d8cef6782e6ba68dcb285fb14&imgtype=0&src=http%3A%2F%2Fb.zol-img.com.cn%2Fdesk%2Fbizhi%2Fimage%2F6%2F1920x1080%2F1432000588540.jpg
---
# 概览
Recoil lets you create a data-flow graph that flows from atoms (shared state) through selectors (pure functions) and down into your React components. Atoms are units of state that components can subscribe to. Selectors transform this state either synchronously or asynchronously.
Recoil可以通过Selector(纯函数)和Atoms(共享状态)来创建组件之间共享数据的数据流图。Atoms是组件可以订阅到的状态单元。Selector可以是同步也可以是异步。
### Atoms
Atoms are units of state. They're updateable and subscribable: when an atom is updated, each subscribed component is re-rendered with the new value. They can be created at runtime, too. Atoms can be used in place of React local component state. If the same atom is used from multiple components, all those components share their state.Atoms are created using the atom function:
Atoms就是状态单元，他们是可更新和可订阅的。一但某一个Atom更新，那么订阅他的组件就会得到新值重新渲染。这些Atoms可以在运行时被创建，也可在局部组件中使用。如果同一个Atom被多个组件使用，那么这些组件就会共享这个Atom.Atom可以通过Atom函数来创建：
```
const fontSizeState = atom({
    key: 'fontSizeState',
    default: 14,
});
```
