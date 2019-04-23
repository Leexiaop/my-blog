---
layout: conformance
title: 标准规范
date: 2019-01-09 15:17:37
tags:
---
A conforming implementation of ECMAScript must provide and support all the types, values, objects, properties, functions, and program syntax and semantics described in this specification.
符合规范的ECMAScript实现必须提供并支持本规范中描述的所有类型、值、对象、属性、函数以及程序语法和语义。

A conforming implementation of ECMAScript must interpret source text input in conformance with the latest version of the Unicode Standard and ISO/IEC 10646.
符合ECMAScript的实现必须按照Unicode标准和ISO/IEC 10646的最新版本解释源文本输入。

A conforming implementation of ECMAScript that provides an application programming interface (API) that supports programs that need to adapt to the linguistic and cultural conventions used by different human languages and countries must implement the interface defined by the most recent edition of ECMA-402 that is compatible with this specification.
ECMAScript的一致性实现提供了一个应用程序编程接口(API)，该接口支持需要适应不同人类语言和国家使用的语言和文化惯例的程序，并且必须实现与此规范兼容的ECMA-402最新版本定义的接口。

A conforming implementation of ECMAScript may provide additional types, values, objects, properties, and functions beyond those described in this specification. In particular, a conforming implementation of ECMAScript may provide properties not described in this specification, and values for those properties, for objects that are described in this specification.
符合规范的ECMAScript实现可以提供超出本规范中描述的其他类型、值、对象、属性和函数。特别是，符合规范的ECMAScript实现可能提供本规范中没有描述的属性，以及那些属性的值，以及本规范中描述的对象的值。

A conforming implementation of ECMAScript may support program and regular expression syntax not described in this specification. In particular, a conforming implementation of ECMAScript may support program syntax that makes use of the “future reserved words” listed in subclause 11.6.2.2 of this specification.
符合规范的ECMAScript实现可能支持本规范中没有描述的程序和正则表达式语法。特别是，符合规范的ECMAScript实现可能支持使用本规范第11.6.2.2小节中列出的“保留字符”的程序语法。

A conforming implementation of ECMAScript must not implement any extension that is listed as a Forbidden Extension in subclause 16.2.
符合规范的ECMAScript的实现不能实现第16.2款中列出的任何禁止扩展。