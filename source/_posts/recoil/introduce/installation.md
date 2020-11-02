---
title: Recoil简介--安装教程
date: 2020-10-28 18:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1603965553359&di=81c46022342b14d246d6e38d9401b19d&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2F2018-11-02%2F5bdc0bd998b68.jpg
---
### NPM
The Recoil package lives in npm. To install the latest stable version, run the following command:
Recoil包已经发不到npm,可以通过下列命令安装最新的最稳定的版本：
```
npm install recoil
```
或者是用yarn
```
yarn add recoil
```
### Bundler
Recoil installed via NPM pairs nicely with module bundlers such as Webpack or Rollup.
就像webpack或者Rollup一样，Recoil可以搭配模块坏bundlers通过npm安装。
### ES5 support ES5支持
Recoil builds are not transpiled to ES5, and we do not support the use of Recoil with ES5. If you need to support browsers that do not provide ES6 features natively, you can do so by compiling your code with Babel and using preset @babel/preset-env. However, we do not support this and you may run into problems.
Recoil的构建不能转化支持ES5，所以我们并不建议使用ES5。如果你需要兼容不支持ES6的本地浏览器。你也可以通过安装@babel/preset-env插件的方式通过babel转译来实现，但是我们并不支持这么做，这可能会出现问题。

In particular, just like React, Recoil depends on the Map and Set types and other features of ES6. Emulation of these features using polyfills may result in far worse performance.
就像React一样，Recoil依赖于Map或者Set类型的ES6语法，但是如果通过使用polyfills来达到这一目的也许会得到事倍功半的效果。
### CDN
Since version 0.0.11, Recoil offers a UMD build that can be directly used in a `<script>` tag and exposes the symbol Recoil to the global namespace. We recommend linking to a specific version number and build to avoid unexpected breakage from newer versions:
从0.0.11版本开始，Recoil提供了UMD的构建方式，可以直接使用`<script>`标签,将Recoil暴露到全局。我们推荐引入一个具体版本，以免被收到新版本的影响。
```
<script src="https://cdn.jsdelivr.net/npm/recoil@0.0.11/umd/recoil.production.js"></script>
```
You can browse all Recoil files on the CDN at [jsdelivr](https://www.jsdelivr.com/package/npm/recoi).
可以 通过CDN下载所有有关[Recoil的资料](https://www.jsdelivr.com/package/npm/recoil)。
### ESLint
If you are using [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks) in your project. For example, with an eslint config like this:
如果你在你的项目里使用了[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)插件，例如，eslint的配置是这样：
```
// previous .eslint config
{
    "plugins": [
        "react-hooks"
    ],
    "rules": {
        "react-hooks/rules-of-hooks": "error",
        "react-hooks/exhaustive-deps": "warn"
    }
}
```
It is recommended to add ['useRecoilCallback'](https://recoiljs.org/docs/api-reference/core/useRecoilCallback) to the list of additionalHooks. With this change, ESLint will warn when the dependencies passed to useRecoilCallback() are specified incorrectly and suggests a fix. The format of additionalHooks is a regex string.
推荐在运用hooks的列表中添加[`useRecoilCallback`](https://recoiljs.org/docs/api-reference/core/useRecoilCallback)，有了这个操作，eslint就会在有参数传入`useRecoilCallback()`函数后执行检查是否正确，并提示修复。hooks的格式是一个正则表达式。
```
// modified .eslint config
{
    "plugins": [
        "react-hooks"
    ],
    "rules": {
        "react-hooks/rules-of-hooks": "error",
        "react-hooks/exhaustive-deps": [
            "warn", {
                "additionalHooks": "useRecoilCallback"
            }
        ]
    }
}
```
### Nightly Builds Nightly构建
We build a package once every day based on the current master branch and publish it as the nightly branch on GitHub. You can use this branch via npm:
我们每天会依赖最新的master分支构建一次，并且以nightly分支发布到Github上，可以通过这个分支使用npm安装：
```
npm install https://github.com/facebookexperimental/Recoil.git#nightly
```
yarn：
```
yarn add https://github.com/facebookexperimental/Recoil.git#nightly
```
or add a dependency in package.json and run npm install or yarn:

或者是在package.json中添加依赖，并且通过命令`npm install` 或者`yarn`安装：
```
"recoil": "facebookexperimental/Recoil.git#nightly",
```