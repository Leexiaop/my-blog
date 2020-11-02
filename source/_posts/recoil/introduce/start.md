---
title: Recoil简介--开始应用
date: 2020-10-28 18:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1603965596353&di=50e1f05c47902c9d6c1e54d6cb181f83&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2F2%2F59a64e5053dc3.jpg
---
# Getting Started

### Create React App 创建应用
Recoil is a state management library for React, so you need to have React installed and running to use Recoil. The easiest and recommended way for bootstrapping a React application is to use [Create React App](https://github.com/facebook/create-react-app#creating-an-app):
Recoil是一个为Reactk开发的状态管理库，所以运行Recoil的先安装React,推荐最简单的安装React应用方式就是用如下命令：
```
npx create-react-app my-app
```
> [npx]() is a package runner tool that comes with npm 5.2+ and higher, see instructions for older npm versions.
npx是一个npm5.2或更高版本的运行工具包，[点击查看低版本的npm说明]()。

For more ways to install Create React App, see the [official documentation](https://github.com/facebook/create-react-app#creating-an-app).
更多安装应用方式请查看[官方文档](https://github.com/facebook/create-react-app#creating-an-app)。
### Installation 安装
The Recoil package lives in npm. To install the latest stable version, run the following command:
Recoil包已经发布到npm上，安装最新最稳定的版本只要运行以下命令：
```
npm install recoil
```
Or if you're using yarn:
或者使用yarn:
```
yarn add recoil
```
### RecoilRoot
Components that use recoil state need `RecoilRoot` to appear somewhere in the parent tree. A good place to put this is in your root component:
组件中使用Recoil状态需要在父级中使用`RecoilRoot`,组件中最好的方式就是将`RecoilRoot`放到根组件中。
```
import React from 'react';
import {
    RecoilRoot,
    atom,
    selector,
    useRecoilState,
    useRecoilValue,
} from 'recoil';

function App() {
    return (
        <RecoilRoot>
        <CharacterCounter />
        </RecoilRoot>
    );
}
```
We'll implement the `CharacterCounter` component in the following section.
接下来我们会开发一个`CharacterCounter`的组件。
### Atom
An **atom** represents a piece of **state**. Atoms can be read from and written to from any component. Components that read the value of an atom are implicitly **subscribed** to that atom, so any atom updates will result in a re-render of all components subscribed to that atom:
一个Atom就代表一个state,Atom可以被任何一个组件读取，更新。组件读取 Atom 数据将会隐式订阅它，任何更新都会导致订阅它的组件进行重新渲染。
```
const textState = atom({
    key: 'textState', // unique ID (with respect to other atoms/selectors)
    default: '', // default value (aka initial value)
});
```
Components that need to read from and write to an atom should use useRecoilState() as shown below:
在组件中使用 useRecoilState() 读写 Atom 数据：
```
function CharacterCounter() {
    return (
        <div>
        <TextInput />
        <CharacterCount />
        </div>
    );
}

function TextInput() {
  const [text, setText] = useRecoilState(textState);

  const onChange = (event) => {
    setText(event.target.value);
  };

  return (
    <div>
        <input type="text" value={text} onChange={onChange} />
        <br />
        Echo: {text}
    </div>
  );
}
```
### Selector
A **selector** represents a piece of **derived state**. Derived state is a **transformation** of state. You can think of derived state as the output of passing state to a pure function that modifies the given state in some way:
selectore代表派生状态。Derived state是状态的一种转化，在某种意义上你可以认为derived state是纯函数输出的某种state.
```
const charCountState = selector({
    key: 'charCountState', // unique ID (with respect to other atoms/selectors)
    get: ({get}) => {
        const text = get(textState);

        return text.length;
    },
});
```
We can use the `useRecoilValue()` hook to read the value of `charCountState`:
我们可以用`useRecoilValue()`钩子来读取`charCountState`的值：
```
function CharacterCount() {
    const count = useRecoilValue(charCountState);

    return <>Character Count: {count}</>;
}
```
### Demo
略