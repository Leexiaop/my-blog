---
title: Recoil指南--Asynchronous Data Queries 异步数据请求
date: 2020-11-9 11:07:13
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604901657888&di=130ab905b130b4ee241473b05b5fd0db&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2Fe%2F598911dca1bfc.jpg
---
Recoil provides a way to map state and derived state to React components via a data-flow graph. What's really powerful is that the functions in the graph can also be asynchronous. This makes it easy to use asynchronous functions in synchronous React component render functions. Recoil allows you to seamlessly mix synchronous and asynchronous functions in your data-flow graph of selectors. Simply return a Promise to a value instead of the value itself from a selector `get` callback, the interface remains exactly the same. Because these are just selectors, other selectors can also depend on them to further transform the data.
Recoil通过数据流图为react提供了俩中state.更为强大的是，函数式的数据流可以是异步的。这使得在同步的react组件中使用异步函数渲染变的非常容易。在Recoil的selector数据流中可以允同步和异步方法混合使用，翻译一个Promise来获取值，而不是Selector的`get`回调中。接口也是同样的类似，因为这些仅仅是Selector,其他的Selector可以依赖于这些Selector做更多的数据转化。
Selectors can be used as one way to incorporate asynchronous data into the Recoil data-flow graph. Please keep in mind that selectors represent "idempotent" functions: For a given set of inputs they should always produce the same results (at least for the lifetime of the application). This is important as selector evaluations may be cached, restarted, or executed multiple times. Because of this, selectors are generally a good way to model read-only DB queries. For mutable data you can use a [Query Refresh](https://recoiljs.org/docs/guides/asynchronous-data-queries#query-refresh) or to synchronize mutable state, persist state, or for other side-effects consider the experimental [Atom Effects API](https://recoiljs.org/docs/guides/atom-effects).
Selector可以被视作为是注册异步数据的一种方式。切记，selector代表了'幂等'函数：对于给定的一组输入，它们应该总是产生相同的结果（至少在应用程序的生命周期内），这一点非常的重要，因为selector的运算可能被缓存，重新计算，或者被执行多次。尽管如此，Selector也是一个读取数据查询非常好的方式。对于那些可变的数据，可以[刷新请求](https://recoiljs.org/docs/guides/asynchronous-data-queries#query-refresh)或者同步可变状态，持久状态或者可以考虑其他的副作用的[Atom Api](https://recoiljs.org/docs/guides/atom-effects).
### Synchronous Example 同步案例
For example, here is a simple synchronous [atom](https://recoiljs.org/docs/api-reference/core/atom) and [selector](https://recoiljs.org/docs/api-reference/core/selector) to get a user name:
例如，下面是一个简单的异步获取用户名的[Atom](https://recoiljs.org/docs/api-reference/core/atom)和[Selector](https://recoiljs.org/docs/api-reference/core/selector)。
```
const currentUserIDState = atom({
    key: 'CurrentUserID',
    default: 1,
});

const currentUserNameState = selector({
    key: 'CurrentUserName',
    get: ({get}) => {
        return tableOfUsers[get(currentUserIDState)].name;
    },
});

function CurrentUserInfo() {
    const userName = useRecoilValue(currentUserNameState);
    return <div>{userName}</div>;
}

function MyApp() {
    return (
        <RecoilRoot>
        <CurrentUserInfo />
        </RecoilRoot>
    );
}
```
### Asynchronous Example 异步案例
If the user names were stored in some database we need to query, all we need to do is return a `Promise` or use an `async` function. If any dependencies change, the selector will be re-evaluated and execute a new query. The results are cached, so the query will only execute once per unique input.
如果我们需要的用户名是被存储到某个数据库，我们需要做的就是就是通过`async`方法返回一个`Promise`.如果一些依赖项发生变化，Selector也会重新计算，或者是重新发起请求。结果会被缓存，所以请求只会被执行一次。
```
const currentUserNameQuery = selector({
    key: 'CurrentUserName',
    get: async ({get}) => {
        const response = await myDBQuery({
            userID: get(currentUserIDState),
        });
        return response.name;
    },
});

function CurrentUserInfo() {
    const userName = useRecoilValue(currentUserNameQuery);
    return <div>{userName}</div>;
}
```
The interface of the selector is the same, so the component using this selector doesn't need to care if it was backed with synchronous atom state, derived selector state, or asynchronous queries!
Selector的接口也是一样的，因此组件中用到Selector并不需要关心是否支持同步的Atom 状态，派生Selector状态，或者是异步请求。
But, since React render functions are synchronous, what will it render before the promise resolves? Recoil is designed to work with [React Suspense](https://reactjs.org/docs/concurrent-mode-suspense.html) to handle pending data. Wrapping your component with a Suspense boundary will catch any descendants that are still pending and render a fallback UI:
但是，由于react的渲染是同步的，那么在promise reslove之前什么会被渲染？Recoil设计使用[react Suspense](https://reactjs.org/docs/concurrent-mode-suspense.html)来处理进行中的数据。将组件以及其后代组件暂时都挂起，返回等待中的UI界面：
```
function MyApp() {
    return (
        <RecoilRoot>
            <React.Suspense fallback={<div>Loading...</div>}>
                <CurrentUserInfo />
            </React.Suspense>
        </RecoilRoot>
    );
}
```
### Error Handling 错误处理
But what if the request has an error? Recoil selectors can also throw errors which will then be thrown if a component tries to use that value. This can be caught with a React [<ErrorBoundary>](https://reactjs.org/docs/error-boundaries.html). For example:
一旦请求出现错误怎么办？Recoil Selector可以扔出一个React组件可以捕获的错误。这个错误可以被React的[<ErrorBoundary>](https://reactjs.org/docs/error-boundaries.html)组件捕获。案例：
```
const currentUserNameQuery = selector({
    key: 'CurrentUserName',
    get: async ({get}) => {
        const response = await myDBQuery({
            userID: get(currentUserIDState),
        });
        if (response.error) {
            throw response.error;
        }
        return response.name;
    },
});

function CurrentUserInfo() {
    const userName = useRecoilValue(currentUserNameQuery);
    return <div>{userName}</div>;
}

function MyApp() {
    return (
        <RecoilRoot>
            <ErrorBoundary>
                <React.Suspense fallback={<div>Loading...</div>}>
                    <CurrentUserInfo />
                </React.Suspense>
            </ErrorBoundary>
        </RecoilRoot>
    );
}
```
### Queries with Parameters 带参请求
Sometimes you want to be able to query based on parameters that aren't just based on derived state. For example, you may want to query based on the component props. You can do that using the [selectorFamily](https://recoiljs.org/docs/api-reference/utils/selectorFamily) helper:
有时候你可能想要发起一个携带参数带请求的不仅仅依赖于派生状态。例如，你的请求是基于组件的props,那么你可以使用[selectorFamily](https://recoiljs.org/docs/api-reference/utils/selectorFamily)：
```
const userNameQuery = selectorFamily({
    key: 'UserName',
    get: userID => async () => {
        const response = await myDBQuery({userID});
        if (response.error) {
            throw response.error;
        }
        return response.name;
    },
});

function UserInfo({userID}) {
    const userName = useRecoilValue(userNameQuery(userID));
    return <div>{userName}</div>;
}

function MyApp() {
    return (
        <RecoilRoot>
            <ErrorBoundary>
                <React.Suspense fallback={<div>Loading...</div>}>
                    <UserInfo userID={1}/>
                    <UserInfo userID={2}/>
                    <UserInfo userID={3}/>
                </React.Suspense>
            </ErrorBoundary>
        </RecoilRoot>
    );
}
```
### Data-Flow Graph 数据流
Remember, by modeling queries as selectors, we can build a data-flow graph mixing state, derived state, and queries! This graph will automatically update and re-render React components as state is updated.
切记，我们可以通过模块查询来创建混合状态，派生状态，以及请求的Selector。这样数据状态一旦更新React的组件会自动更新并重新渲染。
The following example will render the current user's name and a list of their friends. If a friend's name is clicked on, they will become the current user and the name and list will be automatically updated.
下面的例子将会渲染当前的用户名，以及朋友列表，如果某一个朋友的名字被点击。他将会变成当前的用户，并且姓名和裂变会自动更新。
```
const currentUserIDState = atom({
    key: 'CurrentUserID',
    default: null,
});

const userInfoQuery = selectorFamily({
    key: 'UserInfoQuery',
    get: userID => async () => {
        const response = await myDBQuery({userID});
        if (response.error) {
            throw response.error;
        }
        return response;
    },
});

const currentUserInfoQuery = selector({
    key: 'CurrentUserInfoQuery',
    get: ({get}) => get(userInfoQuery(get(currentUserIDState))),
});

const friendsInfoQuery = selector({
    key: 'FriendsInfoQuery',
    get: ({get}) => {
        const {friendList} = get(currentUserInfoQuery);
        return friendList.map(friendID => get(userInfoQuery(friendID)));
    },
});

function CurrentUserInfo() {
    const currentUser = useRecoilValue(currentUserInfoQuery);
    const friends = useRecoilValue(friendsInfoQuery);
    const setCurrentUserID = useSetRecoilState(currentUserIDState);
    return (
        <div>
            <h1>{currentUser.name}</h1>
            <ul>
                {friends.map(friend =>
                <li key={friend.id} onClick={() => setCurrentUserID(friend.id)}>
                    {friend.name}
                </li>
                )}
            </ul>
        </div>
    );
}

function MyApp() {
    return (
        <RecoilRoot>
            <ErrorBoundary>
                <React.Suspense fallback={<div>Loading...</div>}>
                <CurrentUserInfo />
                </React.Suspense>
            </ErrorBoundary>
        </RecoilRoot>
    );
}
```
### Concurrent Requests 当前请求
If you notice in the above example, the `friendsInfoQuery` uses a query to get the info for each friend. But, by doing this in a loop they are essentially serialized. If the lookup is fast, maybe that's ok. If it's expensive, you can use a concurrency helper such as [waitForAll](https://recoiljs.org/docs/api-reference/utils/waitForAll) to run them in parallel. This helper accepts both arrays and named objects of dependencies.
如果你注意到上面到例子。`friendsInfoQuery`发起请求来获取每一个朋友的信息。但是通过这样的循环是为了本质上的有序列化。如果查询速度够快，这没什么，要不然就，可以使用例如[waitForAll](https://recoiljs.org/docs/api-reference/utils/waitForAll)的并发请求来处理。他既可以接受数组，也可以接受对象作为参数。
```
const friendsInfoQuery = selector({
    key: 'FriendsInfoQuery',
    get: ({get}) => {
        const {friendList} = get(currentUserInfoQuery);
        const friends = get(waitForAll(
            friendList.map(friendID => userInfoQuery(friendID))
        ));
        return friends;
    },
});
```
You can use [waitForNone](https://recoiljs.org/docs/api-reference/utils/waitForNone) to handle incremental updates to the UI with partial data.
同样也可以使用[waitForNone](https://recoiljs.org/docs/api-reference/utils/waitForNone)部分数据来处理UI层面的更新。
```
const friendsInfoQuery = selector({
    key: 'FriendsInfoQuery',
    get: ({get}) => {
        const {friendList} = get(currentUserInfoQuery);
        const friendLoadables = get(waitForNone(
                friendList.map(friendID => userInfoQuery(friendID))
            ));
        return friendLoadables
        .filter(({state}) => state === 'hasValue')
        .map(({contents}) => contents);
    },
});
```
### Pre-Fetching 预请求
For performance reasons you may wish to kick off fetching before rendering. That way the query can be going while we start rendering. The [React docs](https://reactjs.org/docs/concurrent-mode-suspense.html#start-fetching-early) give some examples. This pattern works with Recoil as well.
由于一些其他的原因，你可能希望能在渲染之前开启请求。这当然也是没有问题的。在开始渲染的时候，是可以发起请求的。React文档给了我们例子，这当然也适用于Recoil.
Let's change the above example to initiate a fetch for the next user info as soon as the user clicks the button to change users:
我们来修改一下上面的例子，在用户一点击按钮改变用户的时候为下一个用户初始化一个请求。
```
function CurrentUserInfo() {
    const currentUser = useRecoilValue(currentUserInfoQuery);
    const friends = useRecoilValue(friendsInfoQuery);

    const changeUser = useRecoilCallback(({snapshot, set}) => userID => {
        snapshot.getLoadable(userInfoQuery(userID)); // pre-fetch user info
        set(currentUserIDState, userID); // change current user to start new render
    });

    return (
        <div>
            <h1>{currentUser.name}</h1>
            <ul>
                {friends.map(friend =>
                <li key={friend.id} onClick={() => changeUser(friend.id)}>
                    {friend.name}
                </li>
                )}
            </ul>
        </div>
    );
}
```
### Async Queries Without React Suspense 没有React Suspense的异步请求
It is not necessary to use React Suspense for handling pending asynchronous selectors. You can also use the [useRecoilValueLoadable()](https://recoiljs.org/docs/api-reference/core/useRecoilValueLoadable) hook to determine the status during rendering:
使用React Subspense来处理进行中的异步Selector，并不是必须的。在渲染期间，也可以使用[useRecoilValueLoadable()](https://recoiljs.org/docs/api-reference/core/useRecoilValueLoadable)钩子函数来处理状态。
```
function UserInfo({userID}) {
    const userNameLoadable = useRecoilValueLoadable(userNameQuery(userID));
    switch (userNameLoadable.state) {
        case 'hasValue':
            return <div>{userNameLoadable.contents}</div>;
        case 'loading':
            return <div>Loading...</div>;
        case 'hasError':
            throw userNameLoadable.contents;
    }
}
```
### Query Refresh请求刷新
When using selectors to model data queries, it's important to remember that selector evaluation should always provide a consistent value for a given state. Selectors represent state derived from other atom and selector states. Thus, selector evaluation functions should be idempotent for a given input, as it may be cached or executed multiple times. Practically, that means a single selector should not be used for a query where you expect the results to vary during the application's lifetime.
当使用Selector来发起数据请求的时候，切记，selector运算总是会提供和给定的状态一致的值。Selector代表状态派生到其他的Atom或者是selector状态。因此，对于给定的输入，Selector函数应该是幂等的。因为他可能会被缓存，或者是执行多次。实际上，这意味着一个Selector在整个应用的生命周期中不应该被应用于你希望得到的结果总是变化的请求。

There are a few patterns you can use for working with mutable data:
下面是一些应用于修改数据的模式：
##### Use a Request ID 用ID请求
Selector evaluation should provide a consistent value for a given state based on input (dependent state or family parameters). So, you could add a request ID as either a family parameter or a dependency to your query. For example:
Selector运算需要为state提供基于输入（独立的状态或者是一组参数）一致的值，所以，需要为请求添加一个请求的ID，要不是参数，要不是依赖，例如：
```
const userInfoQueryRequestIDState = atomFamily({
    key: 'UserInfoQueryRequestID',
    default: 0,
});

const userInfoQuery = selectorFamily({
    key: 'UserInfoQuery',
    get: userID => async ({get}) => {
        get(userInfoQueryRequestIDState(userID)); // Add request ID as a dependency
        const response = await myDBQuery({userID});
        if (response.error) {
            throw response.error;
        }
        return response;
    },
});

function useRefreshUserInfo(userID) {
    setUserInfoQueryRequestID = useSetRecoilState(userInfoQueryRequestIDState(userID));
    return () => {
        setUserInfoQueryRequestID(requestID => requestID++);
    };
}

function CurrentUserInfo() {
    const currentUserID = useRecoilValue(currentUserIDState);
    const currentUserInfo = userRecoilValue(userInfoQuery(currentUserID));
    const refreshUserInfo = useRefreshUserInfo(currentUserID);

    return (
        <div>
            <h1>{currentUser.name}</h1>
            <button onClick={refreshUserInfo}>Refresh</button>
        </div>
    );
}
```
##### Use an Atom Atom使用
Another option is to use an atom, instead of a selector, to model the query results. You can imperatively update the atom state with the new query results based on your refresh policy.
另外一个方法就是使用Atom,而不是Selector来处理请求结果。可以根据刷新策略使用新的查询结果强制更新atom状态。
```
const userInfoState = atomFamily({
    key: 'UserInfo',
    default: userID => fetch(userInfoURL(userID)),
});

// React component to refresh query
function RefreshUserInfo({userID}) {
    const refreshUserInfo = useRecoilCallback(({set}) => async id => {
        const userInfo = await myDBQuery({userID});
        set(userInfoState(userID), userInfo);
    }, [userID]);

    // Refresh user info every second
    useEffect(() => {
        const intervalID = setInterval(refreshUserInfo, 1000);
        return () => clearInterval(intervalID);
    }, [refreshUserInfo]);

    return null;
}
```
One downside to this approach is that atoms do not currently support accepting a Promise as the new value in order to automatically take advantage of React Suspense while the query refresh is pending, if that is your desired behavior. However, you could store an object which manually encodes the loading status as well as the results if desired.
这种方法的一个缺点是atoms当前不支持接受Promise作为新值，以便在查询刷新挂起时自动利用React-Suspense（如果这是您想要的行为）。但是，如果需要，您可以存储一个手动编码加载状态和结果的对象。

Also consider [atom effects](https://recoiljs.org/docs/guides/atom-effects) for query synchronization of atoms.
同样需求考虑[Atom作用](https://recoiljs.org/docs/guides/atom-effects)对atom同步请求的影响。