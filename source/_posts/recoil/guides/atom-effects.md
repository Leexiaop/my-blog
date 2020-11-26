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
Atom副作用主要是通过React的`useEffect`钩子函数来生效。然后atoms是在React上下文之外被创建的。并且在React组建内部管理副作用是非常困难的。特别是动态生成的atoms.这些atoms也常常并不能被用来初始化初始的atom值，或者是用来做服务端渲染。通常atom副作是和定义atom在同一处。
```
const myState = atom({key: 'Key', default: null});

function MyStateEffect(): React.Node {
    const [value, setValue] = useRecoilState(myState);
    useEffect(() => {
        // Called when the atom value changes
        store.set(value);
        store.onChange(setValue);
        return () => { store.onChange(null); }; // Cleanup effect
    }, [value]);
    return null;
}

function MyApp(): React.Node {
    return (
        <div>
            <MyStateEffect />
            ...
        </div>
    );
}
```
#### Compared to Snapshots
The [Snapshot hooks](https://www.recoiljs.cn/docs/api-reference/core/Snapshot#hooks) API can also monitor atom state changes and the initializeState prop in [<RecoilRoot>](https://www.recoiljs.cn/docs/api-reference/core/RecoilRoot) can initialize values for initial render. However, these APIs monitor all state changes and can be awkward to manage dynamic atoms, particularly atom families. With atom effects, the side-effect can be defined per-atom alongside the atom definition and multiple policies can be easily composed.
Snapshot hooks钩子API可以监听Atom状态的变化并且为初次渲染初始化RecoilRoot组件的初始值。然后，这些API监听所有的状态变化，并且很难管理动态的Atom.尤其是atom系列。有了atom副作用。一些副作用，就可以通过atom提前定义，并且和多种方案组合在一起。
#### Logging Example
A simple example of using atom effects are for logging a specific atom's state changes.
一个简单的使用atom副作用来记录特定atom状态的变化：
```
const currentUserIDState = atom({
    key: 'CurrentUserID',
    default: null,
    effects_UNSTABLE: [
        ({onSet}) => {
        onSet(newID => {
            console.debug("Current user ID:", newID);
        });
        },
    ],
});
```
#### History Example
A more complex example of logging might maintain a history of changes. This example provides an effect which maintains a history queue of state changes with callback handlers that undo that particular change:
一个更复杂的例子是用来维护变化的历史记录。此示例提供了一个效果，它使用撤消特定更改的回调处理程序维护状态更改的历史队列：
```
const history: Array<{
    label: string,
    undo: () => void,
}> = [];

const historyEffect = name => ({setSelf, onSet}) => {
    onSet((newValue, oldValue) => {
        history.push({
            label: `${name}: ${JSON.serialize(oldValue)} -> ${JSON.serialize(newValue)}`,
            undo: () => {
                setSelf(oldValue);
            },
        });
    });
};

const userInfoState = atomFamily({
    key: 'UserInfo',
    default: null,
    effects_UNSTABLE: userID => [
        historyEffect(`${userID} user info`),
    ],
});
```
#### State Synchronization Example 状态同步案例
It can be useful to use atoms as a local cached value of some other state such as a remote database, local storage, &c. You could set the `default` value of an atom using the `default` property with a selector to get the store's value. However, that is only a one-time lookup; if the store's value changes the atom value will not change. With effects, we can subscribe to the store and update the atom's value whenever the store changes. Calling `setSelf()` from the effect will initialize the atom to that value and will be used for the initial render. If the atom is reset, it will revert to the default value, not the initialized value.
将atom作为诸如，远程数据库，本地存储等的本地缓存状态是非常有用的。可以通过selector设置默认属性给atom设置默认的值来获取存储的值。然后，这只是一次性的。如果存储中的值发生了变化，而atom的值是不会有变化的。通过副作用，无论何时，我们都可以订阅存储中的值，并且更新atom的值。从副作用中调用`setSelf()`函数来初始化atom并且初次渲染。如果atom被重置。他将会再次被设置为默认值，而不是初始值。
```
const syncStorageEffect = userID => ({setSelf, trigger}) => {
    // Initialize atom value to the remote storage state
    if (trigger === 'get') { // Avoid expensive initialization
        setSelf(myRemoteStorage.get(userID)); // Call synchronously to initialize
    }

    // Subscribe to remote storage changes and update the atom value
    myRemoteStorage.onChange(userID, userInfo => {
        setSelf(userInfo); // Call asynchronously to change value
    });

    // Cleanup remote storage subscription
    return () => {
        myRemoteStorage.onChange(userID, null);
    };
};

const userInfoState = atomFamily({
    key: 'UserInfo',
    default: null,
    effects_UNSTABLE: userID => [
        historyEffect(`${userID} user info`),
        syncStorageEffect(userID),
    ],
});
```
#### Write-Through Cache Example
We can also bi-directionally sync atom values with remote storage so changes on the server update the atom value and changes in the local atom are written back to the server. The effect will not call the `onSet()` handler when changed via that effect's `setSelf()` to help avoid feedback loops.