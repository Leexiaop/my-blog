---
title: Recoil指南--Dev Tools 开发工具
date: 2020-11-28 11:07:13
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1606629692000&di=5657131a9cf6e58c9871cc25bf119d3f&imgtype=0&src=http%3A%2F%2Fattach.bbs.miui.com%2Fforum%2F201404%2F16%2F085754clksjcsljklmhk5s.jpg
---
#### Dev Tools 开发工具
Recoil has functionality to allow you to observe and update state changes.
Recoil可以允许您监听并且更新状态变化。
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#### IMPORTANT NOTE 重点关注
This API is currently under development and will change. Please stay tuned...
这个API当前还在开发中，并且会有变化，请稍等...
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#### Observing All State Changes 监听所有的状态变化
You can use a hook such as [useRecoilSnapshot()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilSnapshot) or [useRecoilTransactionObserver_UNSTABLE()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilTransactionObserver) to subscribe to state changes and obtain a [Snapshot](https://www.recoiljs.cn/docs/api-reference/core/Snapshot) of the new state.
可以使用诸如[useRecoilSnapshot()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilSnapshot)或者[useRecoilTransactionObserver_UNSTABLE()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilTransactionObserver)的钩子函数来订阅状态的变化，或者是添加一个[Snapshot](https://www.recoiljs.cn/docs/api-reference/core/Snapshot)的新状态。

Once you have a `Snapshot`, you can use methods such as `getLoadable()`, `getPromise()`, and `getInfo()` to inspect the state and use `getNodes()` to iterate over the set of known atoms.
一旦使用Snapshot,你就可以用类似于`getLoadable()`, `getPromise()`, 和 `getInfo()`方法去检查state,用`getNodes()`方法来迭代atoms.
```
function DebugObserver(): React.Node {
    const snapshot = useRecoilSnapshot();
    useEffect(() => {
        console.debug('The following atoms were modified:');
        for (const node of snapshot.getNodes({modified: true})) {
        console.debug(node.key, snapshot.getLoadable(node));
        }
    }, [snapshot]);

    return null;
}
```
```
function MyApp() {
    return (
        <RecoilRoot>
            <DebugObserver />
            ...
        </RecoilRoot>
    );
}
```
#### Observing State Changes On-Demand 按需监听state的变化
Or, you can use the [useRecoilCallback()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilCallback) hook to obtain a [Snapshot](https://www.recoiljs.cn/docs/api-reference/core/Snapshot) on-demand.
可以使用使用[useRecoilCallback()](https://www.recoiljs.cn/docs/api-reference/core/useRecoilCallback)hook函数来获取按需[Snapshot](https://www.recoiljs.cn/docs/api-reference/core/Snapshot)。
```
function DebugButton(): React.Node {
    const onClick = useRecoilCallback(({snapshot}) => async () => {
        console.debug('Atom values:');
        for (const node of snapshot.getNodes()) {
            const value = await snapshot.getPromise(node);
            console.debug(node.key, value);
        }
    }, []);

    return <button onClick={onClick}>Dump State</button>
}
```
#### Time Travel
The [useGotoRecoilSnapshot()](https://www.recoiljs.cn/docs/api-reference/core/useGotoRecoilSnapshot) hook can be used to update the entire Recoil state to match the provided `Snapshot`. This example maintains a history of state changes with the ability to go back and restore previous global state.
[useGotoRecoilSnapshot()](https://www.recoiljs.cn/docs/api-reference/core/useGotoRecoilSnapshot) 钩子函数可以用来更新全部的Recoil状态来匹配所提供的`Snapshot`,此示例维护了状态更改的历史，并能够返回和恢复以前的全局状态。
`Snapshot's` also provide a `getID()` method. That can be used, for example, to help determine if you are reverting to a previous known state to avoid updating your snapshot history.
Snapshot同样提供了`getID()`的方法。例如，可以使用它来帮助确定您是否正在恢复到以前的已知状态，以避免更新快照历史记录。
```
function TimeTravelObserver() {
    const [snapshots, setSnapshots] = useState([]);

    const snapshot = useRecoilSnapshot();
    useEffect(() => {
            if (snapshots.every(s => s.getID() !== snapshot.getID())) {
            setSnapshots([...snapshots, snapshot]);
        }
    }, [snapshot]);

    const gotoSnapshot = useGotoRecoilSnapshot();

    return (
        <ol>
        {snapshots.map((snapshot, i) => (
            <li key={i}>
                Snapshot {i}
                <button onClick={() => gotoSnapshot(snapshot)}>
                    Restore
                </button>
            </li>
        ))}
        </ol>
    );
}
```


