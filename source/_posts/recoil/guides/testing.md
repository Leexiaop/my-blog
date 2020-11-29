---
title: Recoil指南--Testing 测试
date: 2020-11-26 16:07:13
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1606477974564&di=bf0b327b23af2e4fd7d0e105ba555257&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2Fd%2F589eb09c5e21c.jpg
---
#### Testing Recoil Selectors outside of React React之外测试Recoil的Selectors
It can be useful to manipulate and evaluate Recoil selectors outside of a React context for testing. This can be done by working with a Recoil [Snapshot](https://www.recoiljs.cn/docs/api-reference/core/Snapshot). You can build a fresh snapshot using `snapshot_UNSTABLE()` and then use that `Snapshot` to evaluate selectors for testing.
脱离React来测试Recoil的Selectors是非常有用的。这可以通过Recolid的Snapshot来实现。可以通过`snapshot_UNSTABLE()`函数来创建一个全新的snapshot，然后用`Snapshot`来实现selector的测试。
#### Example: Jest unit testing selectors Selectors的单元测试
```
const numberState = atom({key: 'Number', default: 0});

const multipliedState = selector({
    key: 'MultipliedNumber',
    get: ({get}) => get(numberState) * 100,
});

test('Test multipliedState', () => {
    const initialSnapshot = snapshot_UNSTABLE();
    expect(mySnapshot.getLoadable(multipliedState).valueOrThrow()).toBe(0);

    const testSnapshot = snapshot_UNSTABLE(({set}) => set(numberState, 1));
    expect(mySnapshot.getLoadable(multipliedState).valueOrThrow()).toBe(100);
})
```