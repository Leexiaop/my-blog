---
title: Recoil API手册--<RecoilRoot />
date: 2020-12-42 10:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1607922256919&di=82c55eb1672802ed643ef2785b3ed6e6&imgtype=0&src=http%3A%2F%2Fn.sinaimg.cn%2Fsinacn07%2F600%2Fw1920h1080%2F20180512%2Fd43c-hamfahw7826410.jpg
---
#### <RecoilRoot ...props />
Provides the context in which atoms have values. Must be an ancestor of any component that uses any Recoil hooks. Multiple roots may co-exist; atoms will have distinct values within each root. If they are nested, the innermost root will completely mask any outer roots.
为atoms提供上下文。必须是所有是使用Recoil hooks组建的根组建，多个根组建也许可以共存，atoms需要对不同的根组建进行区别。如果它们是嵌套的，最里面的根将完全掩盖任何外部根。
Props:
    `initializeState?: (MutableSnapshot => void)`
    An optional function that takes a [MutableSnapshot to](https://recoiljs.org/docs/api-reference/core/Snapshot#transforming-snapshots) [initialize](https://recoiljs.org/docs/api-reference/core/Snapshot#state-initialization) the <RecoilRoot> atom state. This sets up the state for the initial render and is not intended for subsequent state changes or async initialization. Use hooks such as [useSetRecoilState()](https://recoiljs.org/docs/api-reference/core/useSetRecoilState) or [useRecoilCallback()](https://recoiljs.org/docs/api-reference/core/useRecoilCallback) for async state changes.
    通过一个可选函数来初始化根RecoilRoot组建的atom状态。这将设置初始呈现的状态，而不是用于后续状态更改或异步初始化。利用hooks函数如useSetRecoilState()或者useRecoilCallback()实现异步更新状态。

`<RecoilRoot>'s` represent independent providers/stores of atom state. Note that caches, such as selector caches, may be shared across roots. Selector evaluations must be idempotent except for caching or logging, so this should not be a problem, but may be observable or may cause redundant queries to be cached across roots.
<RecoilRoot>组建是atom状态的独立的Provider/store.请注意，缓存（如选择器缓存）可以跨根共享。选择器求值必须是幂等的（除了缓存或日志记录），所以这应该不是问题，但可能是可观察的，或者可能导致在根之间缓存冗余查询。

### 例子
```
import {RecoilRoot} from 'recoil';

function AppRoot() {
    return (
        <RecoilRoot>
            <ComponentThatUsesRecoil />
        </RecoilRoot>
    );
}
```