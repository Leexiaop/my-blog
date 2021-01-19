---
title: Recoil API手册--<RecoilRoot />
date: 2020-12-42 10:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1607922256919&di=82c55eb1672802ed643ef2785b3ed6e6&imgtype=0&src=http%3A%2F%2Fn.sinaimg.cn%2Fsinacn07%2F600%2Fw1920h1080%2F20180512%2Fd43c-hamfahw7826410.jpg
---
#### <RecoilRoot ...props />
Provides the context in which atoms have values. Must be an ancestor of any component that uses any Recoil hooks. Multiple roots may co-exist; atoms will have distinct values within each root. If they are nested, the innermost root will completely mask any outer roots.