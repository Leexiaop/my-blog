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
### Atoms 共享状态
Atoms are units of state. They're updateable and subscribable: when an atom is updated, each subscribed component is re-rendered with the new value. They can be created at runtime, too. Atoms can be used in place of React local component state. If the same atom is used from multiple components, all those components share their state.Atoms are created using the atom function:
Atoms就是状态单元，他们是可更新和可订阅的。一但某一个Atom更新，那么订阅他的组件就会得到新值重新渲染。这些Atoms可以在运行时被创建，也可在局部组件中使用。如果同一个Atom被多个组件使用，那么这些组件就会共享这个Atom.Atom可以通过Atom函数来创建：
```
const fontSizeState = atom({
    key: 'fontSizeState',
    default: 14,
});
```
Atoms need a unique key, which is used for debugging, persistence, and for certain advanced APIs that let you see a map of all atoms. It is an error for two atoms to have the same key, so make sure they're globally unique. Like React component state, they also have a default value.
Atoms需要一个唯一的标示key,用于调试、数据持久化、和确保整个Atom整个系统高阶API正常运行。一旦有俩个Atoms有了相同的key就会报错，所以要确保这个标示全局唯一，就像React组件的状态一样，他们也有自己的默认值。

To read and write an atom from a component, we use a hook called useRecoilState. It's just like React's useState, but now the state can be shared between components:
在组件中开发Atom，会用到useRecoilState这个hooks函数。他就像React中的useState一样，但是运用Atom的hooks函数可以让状态在组件之间共享：
```
function FontButton() {
    const [fontSize, setFontSize] = useRecoilState(fontSizeState);
        return (
            <button onClick={() => setFontSize((size) => size + 1)} style={{fontSize}}>
                Click to Enlarge
            </button>
    );
}
```
Clicking on the button will increase the font size of the button by one. But now some other component can also use the same font size:
点击按钮就会增加按钮中字体的大小，但是这样在其他组件中用到同样字体大小的时候，其他组件的字体大小也会变化：
```
function Text() {
    const [fontSize, setFontSize] = useRecoilState(fontSizeState);
    return <p style={{fontSize}}>This text will increase in size too.</p>;
}
```
### Selectors 纯函数
A selector is a pure function that accepts atoms or other selectors as input. When these upstream atoms or selectors are updated, the selector function will be re-evaluated. Components can subscribe to selectors just like atoms, and will then be re-rendered when the selectors change.
Selector是一个以Atom和其他的Selector为参数的纯函数。当这些上游的Atoms和Selector更新的时候。该Selecotor就会重新计算。组件也可以像Atom一样订阅Selector.当Selector发生变化的时候，组件也会被重新渲染。
Selectors are used to calculate derived data that is based on state. This lets us avoid redundant state, usually obviating the need for reducers to keep state in sync and valid. Instead, a minimal set of state is stored in atoms, while everything else is efficiently computed as a function of that minimal state. Since selectors keep track of what components need them and what state they depend on, they make this functional approach more efficient.
Selector被用来基于原始State的派生数据的计算。因为不需要使用 reducer 来保证数据的一致性和有效性，所以可以避免冗余数据。而是数据都是通过基于存储在Atom上的原始数据计算得到的。由于 Selector 会追踪使用它们的组件以及它们依赖的数据状态，所以函数式编程会比较高效。
From the point of view of components, selectors and atoms have the same interface and can therefore be substituted for one another.
因为 Seletor 和 Atom 给组件提供相同的方法，所以它们可以相互替代。
Selectors are defined using the selector function:
Selector可以通过Selector函数创建：
```
const fontSizeLabelState = selector({
    key: 'fontSizeLabelState',
    get: ({get}) => {
        const fontSize = get(fontSizeState);
        const unit = 'px';
        return `${fontSize}${unit}`;
    },
});
```
The <font color=#D2691E>get</font> property is the function that is to be computed. It can access the value of atoms and other selectors using the get argument passed to it. Whenever it accesses another atom or selector, a dependency relationship is created such that updating the other atom or selector will cause this one to be recomputed.
get属性是一个用于计算的函数。它可以使用入参 get 字段来访问输入的 Atom 和 Selector。当它访问其他 Atom 和 Selector 时，这层依赖关系会保证更新状态的同步。
In this fontSizeLabelState example, the selector has one dependency: the fontSizeState atom. Conceptually, the fontSizeLabelState selector behaves like a pure function that takes a fontSizeState as input and returns a formatted font size label as output.

