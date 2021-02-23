---
title: Recoil API手册--atom(options)
date: 2021-02-22 09:03:09
categories: recoil
tags: recoil
---
An atom represents state in Recoil. The `atom()` function returns a writeable `RecoilState` object.
atom代表了Recoil中的状态，`atom()`返回了一个可读写的Recoil状态对象。

- - -
```
function atom<T>({
    key: string,
    default: T | Promise<T> | RecoilValue<T>,
    effects_UNSTABLE?: $ReadOnlyArray<AtomEffect<T>>,
    dangerouslyAllowMutability?: boolean,
}): RecoilState<T>
```
+ `key` - A unique string used to identify the atom internally. This string should be unique with respect to other atoms and selectors in the entire application.
`key`字段是一个用来定义内部atom的唯一的字符串，这个是字符串必须是唯一的，用来区分应用程序中所有的atoms和selector.
+ `default` - The initial value of the atom or a Promise or another atom or selector representing a value of the same type.
`default`字段用来初始化一个atom或者Promise或者另一个atom或者是selector具有相同类型的初始值。
+ `effects_UNSTABLE` - An optional array of Atom Effects for the atom.
`effects_UNSTABLE`字段一个可选的atom副作用数组。
+ `dangerouslyAllowMutability` - In some cases it may be desireable allow mutating of objects stored in atoms that don't represent state changes. Use this option to override freezing objects in development mode.
`dangerouslyAllowMutability`在某些情况下，他是用来描述允许atoms存储的对象发生转化，但是并不代表状态会发生变化。可以用这个选项在开发环境来冻结对象。
- - -

Recoil manages atom state changes to know when to notify components subscribing to that atom to re-render, so you should use the hooks listed below to change atom state. If an object stored in an atom was mutated directly it may bypass this and cause state changes without properly notifying subscribing components. To help detect bugs Recoil will freeze objects stored in atoms in development mode.

Recoil管理atom状态是通过通知组建订阅atom状态实现重新渲染，所以，应该使用下面的hooks函数来使得atom状态发生变化。如果一个存储在atom中的对象被直接改变，那就会引起组建没有被通知订阅而atom状态发生变化。为了解决这个bug，Recoil会在开发环境冻结存储在atom中的对象。

Most often, you'll use the following hooks to interact with atoms:
通常情况下，需要使用下列的hooks函数来使得atoms发生变化。
+ [useRecoilState()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilState): Use this hook when you intend on both reading and writing to the atom. This hook subscribes the component to the atom.
[useRecoilState()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilState):当你要读写atom的时候使用这个钩子函数，这个钩子函数会订阅组建的atom.
+ [useRecoilValue()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilValue): Use this hook when you intend on only reading the atom. This hook subscribes the component to the atom.
[useRecoilValue()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilValue):当只是读取atom的时候。使用这个钩子函数，这个钩子函数会订阅组建的atom.
+ [useSetRecoilState()](https://www.recoiljs.cn/docs/api-reference/core/useSetRecoilState): Use this hook when you intend on only writing to the atom.
[useSetRecoilState()](https://www.recoiljs.cn/docs/api-reference/core/useSetRecoilState):当只是操作atom的时候。使用这个钩子函数。
+ [useResetRecoilState()](https://www.recoiljs.cn/docs/api-reference/core/useResetRecoilState): Use this hook to reset an atom to its default value.
[useResetRecoilState()](https://www.recoiljs.cn/docs/api-reference/core/useResetRecoilState):当重置atom的默认值的时候，使用这个钩子函数。
For rare cases where you need to read an atom's value without subscribing to the component, see [useRecoilCallback()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilCallback).

对于极少数需要读取atom的值，而不需要订阅组建，可以参考[useRecoilCallback()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilCallback)。

You can initialize an atom either with a static value or with a `Promise` or a `RecoilValue` representing a value of the same type. Because the Promise may be pending or the default selector may be asynchronous it means that the atom value may also be pending or throw an error when reading. Note that you cannot currently assign a `Promise` when setting an atom. Please use [selectors](https://www.recoiljs.cn/docs/api-reference/core/selector) for async functions.

可以用通过一个具有共同类型的静态值或者是一个`Promise`或者是一个`RecoilValue`来初始化atom.因为Promise也许是进行中，或者默认的selector会是异步的，这就会使得atom的状态也是处于进行中或者是抛出一个错误。所以，当是这atom的时候，并不能将当前标注为`Promise`,请使用selector异步函数。

Atoms cannot be used to store `Promise's` or `RecoilValue's` directly, but they may be wrapped in an object. Note that `Promise's` may be mutable. Atoms can be set to a `function`, as long as it is pure, but to do so you may need to use the updater form of setters. (e.g. `set(myAtom, () => myFunc);`).

Atoms不能被直接才存储为`Promise`或者是`RecoilValue's`, 但是他们可以被包裹为一个对象。注意`Promise`可能被改变。Atoms可以被设置为是个函数，只要纯净，就可以使用setters的更新方式。(e.g. `set(myAtom, () => myFunc);`).

案例
```
import {atom, useRecoilState} from 'recoil';

const counter = atom({
    key: 'myCounter',
    default: 0,
});

function Counter() {
    const [count, setCount] = useRecoilState(counter);
    const incrementByOne = () => setCount(count + 1);

    return (
        <div>
            Count: {count}
            <br />
            <button onClick={incrementByOne}>Increment</button>
        </div>
    );
}
```
