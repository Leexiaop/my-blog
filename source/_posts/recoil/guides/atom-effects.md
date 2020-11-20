---
title: Recoil指南--Atom Effects Atom副作用
date: 2020-11-9 18:07:03
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604927354781&di=dc59152681710570285cadf98f3c7dfa&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2F2020-01-14%2F5e1d52cec7163.jpg
---
### Atom Effects Atom副作用
Atom Effects are a new experimental API for managing side-effects and initializing Recoil atoms. They have a variety of useful applications such as state persistence, state synchronization, managing history, logging, &c. They are defined as part of the atom definition, so each atom can specify and compose their own policies. This API is still evolving, and thus marked as `_UNSTABLE`.
Atom Effects是为了管理副作用和初始化Recoil Atom的实验性的API。它会有多种应用，例如：状态持久化，同步状态，历史管理，日志等。这些都被定义为Atom最初定义的一部分。所以每个部分都可以有指定或者组合自己的策略。这些API还在开发中，所以暂时就把它称作`_UNSTABLE`版本吧。

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### IMPORTANT NOTE 重要提示
This API is currently under development and will change. Please stay tuned...
这些API还在开发中。并且会有变化，所以再等等。。。。

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

An atom effect is a function with the following definition.
Atom副作用是下面定义的函数：
```
type AtomEffect<T> = ({
    node: RecoilState<T>, // A reference to the atom itself
    trigger: 'get' | 'set', // The action which triggered initialization of the atom

    // Callbacks to set or reset the value of the atom.
    // This can be called from the atom effect function directly to initialize the
    // initial value of the atom, or asynchronously called later to change it.
    setSelf: (
        | T
        | DefaultValue
        | Promise<T | DefaultValue> // Only allowed for initialization at this time
        | ((T | DefaultValue) => T | DefaultValue),
    ) => void,


    resetSelf: () => void,

    // Subscribe to changes in the atom value.
    // The callback is not called due to changes from this effect's own setSelf().
    onSet: (
        (newValue: T | DefaultValue, oldValue: T | DefaultValue) => void,
    ) => void,

}) => void | () => void; // Optionally return a cleanup handler
```
Atom effects are attached to [atoms](https://www.recoiljs.cn/docs/api-reference/core/atom) via the `effects_UNSTABLE` option. Each atom can reference an array of these atom effect functions which are called in priority order when the atom is initialized. Atoms are initialized when they are used for the first time within a `<RecoilRoot>`, but may be re-initialized again if they were unused and cleaned up. The atom effect function may return an optional cleanup handler to manage cleanup side-effects.
Atom 副作用是通过Atom的`effects_UNSTABLE`来添加的。在Atom初始化的时候。每一个Atom都可以引用一个按照优先级调用的atom副作用函数的数组。当第一次应用于`<RecoilRoot>`时，Atom被初始化。但是也许他们没有被调用或者没有被清除，就会被再次初始化。Atom副作用函数会返回一个可选的处理副作用的函数。
```
const myState = atom({
    key: 'MyKey',
    default: null,
    effects_UNSTABLE: [
        () => {
            ...effect 1...
            return () => ...cleanup effect 1...;
        },
        () => { ...effect 2... },
    ],
});
```
[Atom families](https://www.recoiljs.cn/docs/api-reference/utils/atomFamily) also support parameterized or non-parameterized effects:
Atom也支持参数化或者无参化：
```
const myStateFamily = atomFamily({
    key: 'MyKey',
    default: null,
    effects_UNSTABLE: param => [
        () => {
        ...effect 1 using param...
        return () => ...cleanup effect 1...;
        },
        () => { ...effect 2 using param... },
    ],
});
```
#### Compared to React Effects 与React副作用相比
Atom effects could mostly be implemented via React `useEffect()`. However, the set of atoms are created outside of a React context, and it can be difficult to manage effects from within React components, particularly for dynamically created atoms. They also cannot be used to initialize the initial atom value or be used with server-side rendering. Using atom effects also co-locates the effects with the atom definitions.