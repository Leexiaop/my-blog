---
layout: overview
title: 概览
date: 2019-01-10 18:12:31
tags:
---
This section contains a non-normative overview of the ECMAScript language.
本章是ECMAScript规范的概述。

ECMAScript is an object-oriented programming language for performing computations and manipulating computational objects within a host environment. ECMAScript as defined here is not intended to be computationally self-sufficient; indeed, there are no provisions in this specification for input of external data or output of computed results. Instead, it is expected that the computational environment of an ECMAScript program will provide not only the objects and other facilities described in this specification but also certain environment-specific objects, whose description and behaviour are beyond the scope of this specification except to indicate that they may provide certain properties that can be accessed and certain functions that can be called from an ECMAScript program.
ECMAScript是用于在宿主环境中执行计算和操作计算对象的一种面向对象的编程语言。这里定义的ECMAScript不打算在计算上自给自足;实际上，本规范中没有规定外部数据的输入或计算结果的输出。相反,预计一个ECMAScript程序的计算环境不仅将提供本规范中所描述的对象和其它设施还包括某些特定于环境的对象,其描述和行为超出了本规范的范围除了表明他们可能提供可以访问和某些特定的属性,可以从一个ECMAScript程序调用的函数。

ECMAScript was originally designed to be used as a scripting language, but has become widely used as a general-purpose programming language. A scripting language is a programming language that is used to manipulate, customize, and automate the facilities of an existing system. In such systems, useful functionality is already available through a user interface, and the scripting language is a mechanism for exposing that functionality to program control. In this way, the existing system is said to provide a host environment of objects and facilities, which completes the capabilities of the scripting language. A scripting language is intended for use by both professional and non-professional programmers.
ECMAScript最初被设计为一种脚本语言，但现在已经被广泛用作一种通用编程语言。脚本语言是用于操作、自定义和自动化现有系统的功能的一种编程语言，。在这样的系统中，通过用户界面已经可以使用有用的功能，脚本语言是一种将该功能公开给程序控制的机制。通过这种方式，现有的系统被认为提供了对象和工具的宿主环境，从而完成了脚本语言的功能。脚本语言是为专业和非专业程序员设计的。

ECMAScript was originally designed to be a Web scripting language, providing a mechanism to enliven Web pages in browsers and to perform server computation as part of a Web-based client-server architecture. ECMAScript is now used to provide core scripting capabilities for a variety of host environments. Therefore the core language is specified in this document apart from any particular host environment.
ECMAScript最初被设计成一种Web脚本语言，提供了一种让浏览器中的Web页面活跃起来的机制，并作为基于Web的客户机-服务器体系结构的一部分执行服务器计算。ECMAScript现在用于为各种主机环境提供核心脚本功能。因此，核心语言是在本文档中指定的，与任何特定的主机环境无关。

ECMAScript usage has moved beyond simple scripting and it is now used for the full spectrum of programming tasks in many different environments and scales. As the usage of ECMAScript has expanded, so has the features and facilities it provides. ECMAScript is now a fully featured general-purpose programming language.
ECMAScript的使用已经超越了简单的脚本，现在它被用于许多不同环境和规模的各种编程任务。随着ECMAScript用法的扩展，它提供的特性和功能也在扩展。ECMAScript现在是一种功能齐全的通用编程语言。

Some of the facilities of ECMAScript are similar to those used in other programming languages; in particular C, Java™, Self, and Scheme as described in:
ECMAScript的一些功能与其他编程语言中使用的功能类似;特别是C、Java™、自我和方案中描述:

ISO/IEC 9899:1996, Programming Languages – C.

Gosling, James, Bill Joy and Guy Steele. The Java™ Language Specification. Addison Wesley Publishing Co., 1996.

Ungar, David, and Smith, Randall B. Self: The Power of Simplicity. OOPSLA '87 Conference Proceedings, pp. 227-241, Orlando, FL, October 1987.

IEEE Standard for the Scheme Programming Language. IEEE Std 1178-1990.

# 4.1 Web Scripting Web脚本

A web browser provides an ECMAScript host environment for client-side computation including, for instance, objects that represent windows, menus, pop-ups, dialog boxes, text areas, anchors, frames, history, cookies, and input/output. Further, the host environment provides a means to attach scripting code to events such as change of focus, page and image loading, unloading, error and abort, selection, form submission, and mouse actions. Scripting code appears within the HTML and the displayed page is a combination of user interface elements and fixed and computed text and images. The scripting code is reactive to user interaction, and there is no need for a main program.
web浏览器为客户端计算提供ECMAScript宿主环境，例如，包括表示窗口、菜单、弹出窗口、对话框、文本区域、锚、框架、历史记录、cookie和输入/输出的对象。此外，宿主环境还提供了一种方法，可以将脚本代码附加到诸如焦点更改、页面和图像加载、卸载、错误和中止、选择、表单提交和鼠标操作等事件。脚本代码出现在HTML中，显示的页面是用户界面元素和固定和计算的文本和图像的组合。脚本代码对用户交互是响应的，不需要主程序。

A web server provides a different host environment for server-side computation including objects representing requests, clients, and files; and mechanisms to lock and share data. By using browser-side and server-side scripting together, it is possible to distribute computation between the client and server while providing a customized user interface for a Web-based application.
web服务器为服务器端计算提供了不同的主机环境，包括表示请求、客户机和文件的对象;以及锁定和共享数据的机制。通过同时使用浏览器端和服务器端脚本，可以在客户机和服务器之间分发计算，同时为基于web的应用程序提供定制的用户界面。

Each Web browser and server that supports ECMAScript supplies its own host environment, completing the ECMAScript execution environment.
支持ECMAScript的每个Web浏览器和服务器都提供自己的主机环境，完成ECMAScript执行环境。

# 4.2 ECMAScript Overview

The following is an informal overview of ECMAScript—not all parts of the language are described. This overview is not part of the standard proper.
下面是ECMAScript的非正式概述—并不是描述了该语言的所有部分。这个概述不是标准本身的一部分。

ECMAScript is object-based: basic language and host facilities are provided by objects, and an ECMAScript program is a cluster of communicating objects. In ECMAScript, an object is a collection of zero or more properties each with attributes that determine how each property can be used—for example, when the Writable attribute for a property is set to false, any attempt by executed ECMAScript code to assign a different value to the property fails. Properties are containers that hold other objects, primitive values, or functions. A primitive value is a member of one of the following built-in types: Undefined, Null, Boolean, Number, String, and Symbol; an object is a member of the built-in type Object; and a function is a callable object. A function that is associated with an object via a property is called a method.
ECMAScript是基于由基本提供语言和主机设施对象，ECMAScript程序是一组通信对象。在ECMAScript中，对象是0个或多个属性的集合，每个对象的属性决定了如何使用每个属性——例如，当属性的可写属性设置为false时，执行ECMAScript代码为属性分配不同值的任何尝试都会失败。属性是保存其他对象、基元值或函数的容器。原语值是下列内置类型之一的成员:未定义、空值、布尔值、数字、字符串和符号;对象是内置类型对象的成员;函数是一个可调用对象。通过属性与对象关联的函数称为方法。

ECMAScript defines a collection of built-in objects that round out the definition of ECMAScript entities. These built-in objects include the global object; objects that are fundamental to the runtime semantics of the language including Object, Function, Boolean, Symbol, and various Error objects; objects that represent and manipulate numeric values including Math, Number, and Date; the text processing objects String and RegExp; objects that are indexed collections of values including Array and nine different kinds of Typed Arrays whose elements all have a specific numeric data representation; keyed collections including Map and Set objects; objects supporting structured data including the JSON object, ArrayBuffer, SharedArrayBuffer, and DataView; objects supporting control abstractions including generator functions and Promise objects; and reflection objects including Proxy and Reflect.
ECMAScript定义了一组内置对象，它们完善了ECMAScript实体的定义。这些内置对象包括全局对象;这些对象是语言的运行时语义的基础，包括对象、函数、布尔值、符号和各种错误对象;表示和操作数值(包括数学、数字和日期)的对象;文本处理对象字符串和正则对象，该对象是值的索引集合，包括数组和九种类型数组，其元素都具有特定的数字数据表示形式;键控集合，包括地图和集合对象;支持结构化数据的对象，包括JSON对象、ArrayBuffer、SharedArrayBuffer和DataView;支持控制抽象的对象，包括生成器函数和承诺对象;反射对象包括代理和反射。

ECMAScript also defines a set of built-in operators. ECMAScript operators include various unary operations, multiplicative operators, additive operators, bitwise shift operators, relational operators, equality operators, binary bitwise operators, binary logical operators, assignment operators, and the comma operator.
ECMAScript还定义了一组内置操作符。ECMAScript运算符包括各种一元运算符、乘法运算符、加法运算符、位移运算符、关系运算符、等式运算符、二进制位运算符、二进制逻辑运算符、赋值运算符和逗号运算符。

Large ECMAScript programs are supported by modules which allow a program to be divided into multiple sequences of statements and declarations. Each module explicitly identifies declarations it uses that need to be provided by other modules and which of its declarations are available for use by other modules.
大型ECMAScript程序受一个程序分成多个语句和声明序列模块支持。每个模块显式地标识它使用的声明，这些声明需要由其他模块提供，以及哪些声明可供其他模块使用。

ECMAScript syntax intentionally resembles Java syntax. ECMAScript syntax is relaxed to enable it to serve as an easy-to-use scripting language. For example, a variable is not required to have its type declared nor are types associated with properties, and defined functions are not required to have their declarations appear textually before calls to them.
ECMAScript语法有意地与Java语法相似。ECMAScript语法得到了释放，使其成为一种易于使用的脚本语言。例如，变量的类型不需要声明，类型也不需要与属性关联，并且定义的函数不需要在调用它们之前以文本形式显示它们的声明。

## 4.2.1 Objects 对象

Even though ECMAScript includes syntax for class definitions, ECMAScript objects are not fundamentally class-based such as those in C++, Smalltalk, or Java. Instead objects may be created in various ways including via a literal notation or via constructors which create objects and then execute code that initializes all or part of them by assigning initial values to their properties. Each constructor is a function that has a property named  "prototype" that is used to implement prototype-based inheritance and shared properties. Objects are created by using constructors in new expressions; for example, new Date(2009, 11) creates a new Date object. Invoking a constructor without using new has consequences that depend on the constructor. For example, Date() produces a string representation of the current date and time rather than an object.
尽管ECMAScript包含用于类定义的语法，但ECMAScript对象并不像c++、Smalltalk或Java中的对象那样从根本上基于类。相反，可以通过各种方式创建对象，包括通过文字表示法或通过构造函数创建对象，然后执行通过为其属性赋初值来初始化所有或部分对象的代码。每个构造函数都有一个名为“prototype”的属性，用于实现基于原型的继承和共享属性。对象是通过在新表达式中使用构造函数创建的;例如，new Date(2009,11)创建一个新的Date对象。不使用new而调用构造函数会产生依赖于构造函数的结果。例如，Date()生成当前日期和时间的字符串表示形式，而不是对象。

Every object created by a constructor has an implicit reference (called the object's prototype) to the value of its constructor's "prototype" property. Furthermore, a prototype may have a non-null implicit reference to its prototype, and so on; this is called the prototype chain. When a reference is made to a property in an object, that reference is to the property of that name in the first object in the prototype chain that contains a property of that name. In other words, first the object mentioned directly is examined for such a property; if that object contains the named property, that is the property to which the reference refers; if that object does not contain the named property, the prototype for that object is examined next; and so on.
构造函数创建的每个对象都有一个对其构造函数的“prototype”属性值的隐式引用(称为对象的原型)。此外，原型可能具有对其原型的非空隐式引用，等等;这叫做原型链。当引用对象中的属性时，该引用是对原型链中包含该名称的属性的第一个对象中的该名称的属性的引用。换句话说，首先要检查直接提到的对象是否具有这样的属性;如果该对象包含命名属性，则该属性为引用引用的属性;如果该对象不包含命名属性，则接下来检查该对象的原型;等等。

Figure 1: Object/Prototype Relationships 图1:
![](1547976620354.jpg)

In a class-based object-oriented language, in general, state is carried by instances, methods are carried by classes, and inheritance is only of structure and behaviour. In ECMAScript, the state and methods are carried by objects, while structure, behaviour, and state are all inherited.
一般来说，在基于类的面向对象语言中，状态由实例来承载，方法由类来承载，而继承仅仅是结构和行为的继承。在ECMAScript中，状态和方法由对象携带，而结构、行为和状态都是继承的。

All objects that do not directly contain a particular property that their prototype contains share that property and its value. Figure 1 illustrates this:
所有不直接包含原型包含的特定属性的对象都共享该属性及其值。图1说明了这一点:

CF is a constructor (and also an object). Five objects have been created by using new expressions: cf1, cf2, cf3, cf4, and cf5. Each of these objects contains properties named q1 and q2. The dashed lines represent the implicit prototype relationship; so, for example, cf3's prototype is CFp. The constructor, CF, has two properties itself, named P1 and P2, which are not visible to CFp, cf1, cf2, cf3, cf4, or cf5. The property named CFP1 in CFp is shared by cf1, cf2, cf3, cf4, and cf5 (but not by CF), as are any properties found in CFp's implicit prototype chain that are not named q1, q2, or CFP1. Notice that there is no implicit prototype link between CF and CFp.
CF是一个构造函数(也是一个对象)。使用新的表达式创建了5个对象:cf1、cf2、cf3、cf4和cf5。这些对象都包含名为q1和q2的属性。虚线表示隐式原型关系;例如，cf3的原型是CFp。构造函数CF本身有两个属性P1和P2, CFp、cf1、cf2、cf3、cf4或cf5不可见这两个属性。CFp中名为CFP1的属性由cf1、cf2、cf3、cf4和cf5共享(但不由CF共享)，与CFp隐式原型链中未命名q1、q2或CFP1的任何属性一样。注意，CF和CFp之间没有隐含的原型链接。

Unlike most class-based object languages, properties can be added to objects dynamically by assigning values to them. That is, constructors are not required to name or assign values to all or any of the constructed object's properties. In the above diagram, one could add a new shared property for cf1, cf2, cf3, cf4, and cf5 by assigning a new value to the property in CFp.
与大多数基于类的对象语言不同，属性可以通过赋值动态地添加到对象中。也就是说，构造函数不需要为构造对象的所有或任何属性命名或赋值。在上面的图中，可以为CFp中的cf1、cf2、cf3、cf4和cf5添加一个新的共享属性，方法是为该属性分配一个新值。

Although ECMAScript objects are not inherently class-based, it is often convenient to define class-like abstractions based upon a common pattern of constructor functions, prototype objects, and methods. The ECMAScript built-in objects themselves follow such a class-like pattern. Beginning with ECMAScript 2015, the ECMAScript language includes syntactic class definitions that permit programmers to concisely define objects that conform to the same class-like abstraction pattern used by the built-in objects.
尽管ECMAScript对象本质上不是基于类的，但是基于构造函数、原型对象和方法的公共模式来定义类抽象通常是很方便的。ECMAScript内置对象本身遵循这样一种类模式。从ECMAScript 2015开始，ECMAScript语言包括语法类定义，允许程序员简洁地定义符合内置对象使用的类抽象模式的对象。

## 4.2.2 The Strict Variant of ECMAScript ECMAScript的严格模式

The ECMAScript Language recognizes the possibility that some users of the language may wish to restrict their usage of some features available in the language. They might do so in the interests of security, to avoid what they consider to be error-prone features, to get enhanced error checking, or for other reasons of their choosing. In support of this possibility, ECMAScript defines a strict variant of the language. The strict variant of the language excludes some specific syntactic and semantic features of the regular ECMAScript language and modifies the detailed semantics of some features. The strict variant also specifies additional error conditions that must be reported by throwing error exceptions in situations that are not specified as errors by the non-strict form of the language.
ECMAScript语言认识到，该语言的一些用户可能希望限制使用该语言中可用的一些特性。他们这样做可能是为了安全，为了避免他们认为容易出错的特性，为了得到增强的错误检查，或者出于他们选择的其他原因。为了支持这种可能性，ECMAScript定义了一种严格的语言变体。该语言的严格变体排除了常规ECMAScript语言的一些特定语法和语义特性，并修改了一些特性的详细语义。严格变量还指定了其他错误条件，必须在非严格形式的语言没有指定为错误的情况下抛出错误异常来报告这些错误条件。

The strict variant of ECMAScript is commonly referred to as the strict mode of the language. Strict mode selection and use of the strict mode syntax and semantics of ECMAScript is explicitly made at the level of individual ECMAScript source text units. Because strict mode is selected at the level of a syntactic source text unit, strict mode only imposes restrictions that have local effect within such a source text unit. Strict mode does not restrict or modify any aspect of the ECMAScript semantics that must operate consistently across multiple source text units. A complete ECMAScript program may be composed of both strict mode and non-strict mode ECMAScript source text units. In this case, strict mode only applies when actually executing code that is defined within a strict mode source text unit.
ECMAScript的严格变体通常被称为该语言的严格模式。严格模式的选择和使用ECMAScript的严格模式语法和语义是在每个ECMAScript源文本单元的级别上显式地进行的。因为严格模式是在句法源文本单元的级别上选择的，所以严格模式只施加在源文本单元中具有局部效果的限制。严格模式不限制或修改ECMAScript语义的任何方面，这些方面必须跨多个源文本单元一致地操作。完整的ECMAScript程序可以由严格模式和非严格模式的ECMAScript源文本单元组成。在这种情况下，严格模式仅适用于实际执行在严格模式源文本单元中定义的代码时。

In order to conform to this specification, an ECMAScript implementation must implement both the full unrestricted ECMAScript language and the strict variant of the ECMAScript language as defined by this specification. In addition, an implementation must support the combination of unrestricted and strict mode source text units into a single composite program.
为了符合该规范，ECMAScript实现必须实现完全不受限制的ECMAScript语言和该规范定义的ECMAScript语言的严格变体。此外，实现必须支持将不受限制的和严格模式的源文本单元组合到单个组合程序中。

# 4.3 Terms and Definitions 术语和定义

For the purposes of this document, the following terms and definitions apply.
就本文档而言，适用以下术语和定义。

## 4.3.1 type 类型

set of data values as defined in clause 6 of this specification
如本规范第6条中定义的一组数据值

## 4.3.2 primitive value 原始类型

member of one of the types Undefined, Null, Boolean, Number, Symbol, or String as defined in clause 6
第6条中定义的未定义、Null、Boolean、Number、Symbol或String类型

    NOTE
        A primitive value is a datum that is represented directly at the lowest level of the language implementation.
        原始值是直接在语言实现的最低级别表示的数据。

## 4.3.3 object 对象

member of the type Object 对象

    NOTE
        An object is a collection of properties and has a single prototype object. The prototype may be the null value.
        对象是属性的集合，并且只有一个原型对象。原型可以是空值。

## 4.3.4 constructor 构造函数

function object that creates and initializes objects
函数对象是用来创建和初始化对象

    NOTE
        The value of a constructor's prototype property is a prototype object that is used to implement inheritance and shared properties.
        构造函数的prototype属性的值是一个prototype对象，用于实现继承和共享属性。

## 4.3.5 prototype

object that provides shared properties for other objects
对象是为其他对象提供共享属性

    NOTE
        When a constructor creates an object, that object implicitly references the constructor's prototype property for the purpose of resolving property references. The constructor's prototype property can be referenced by the program expression  constructor.prototype, and properties added to an object's prototype are shared, through inheritance, by all objects sharing the prototype. Alternatively, a new object may be created with an explicitly specified prototype by using the  Object.create built-in function.
        当构造函数创建对象时，该对象隐式引用构造函数的prototype属性，以便解析属性引用。构造函数的prototype属性可以由程序表达式构造函数引用。通过继承，所有共享原型的对象将共享添加到对象原型中的属性。另一种选择是，可以通过使用对象使用显式指定的原型创建新对象。创建内置函数。

## 4.3.6 ordinary object

object that has the default behaviour for the essential internal methods that must be supported by all objects

## 4.3.7 exotic object

object that does not have the default behaviour for one or more of the essential internal methods

    NOTE
        Any object that is not an ordinary object is an exotic object.

## 4.3.8 standard object

object whose semantics are defined by this specification

## 4.3.9 built-in object

object specified and supplied by an ECMAScript implementation

    NOTE
        Standard built-in objects are defined in this specification. An ECMAScript implementation may specify and supply additional kinds of built-in objects. A built-in constructor is a built-in object that is also a constructor.

## 4.3.10 undefined value

primitive value used when a variable has not been assigned a value

## 4.3.11 Undefined type

type whose sole value is the undefined value

## 4.3.12 null value

primitive value that represents the intentional absence of any object value

## 4.3.13 Null type

type whose sole value is the null value

## 4.3.14 Boolean value

member of the Boolean type

    NOTE
        There are only two Boolean values, true and false.

## 4.3.15 Boolean type

type consisting of the primitive values true and false

## 4.3.16Boolean object

member of the Object type that is an instance of the standard built-in Boolean constructor

    NOTE
        A Boolean object is created by using the Boolean constructor in a new expression, supplying a Boolean value as an argument. The resulting object has an internal slot whose value is the Boolean value. A Boolean object can be coerced to a Boolean value.

## 4.3.17 String value

primitive value that is a finite ordered sequence of zero or more 16-bit unsigned integer values

    NOTE
        A String value is a member of the String type. Each integer value in the sequence usually represents a single 16-bit unit of UTF-16 text. However, ECMAScript does not place any restrictions or requirements on the values except that they must be 16-bit unsigned integers.

## 4.3.18 String type

set of all possible String values

## 4.3.19 String object

member of the Object type that is an instance of the standard built-in String constructor

    NOTE
        A String object is created by using the String constructor in a new expression, supplying a String value as an argument. The resulting object has an internal slot whose value is the String value. A String object can be coerced to a String value by calling the String constructor as a function (21.1.1.1).

## 4.3.20 Number value

primitive value corresponding to a double-precision 64-bit binary format IEEE 754-2008 value

    NOTE
        A Number value is a member of the Number type and is a direct representation of a number.

## 4.3.21 Number type

set of all possible Number values including the special “Not-a-Number” (NaN) value, positive infinity, and negative infinity

## 4.3.22 Number object

member of the Object type that is an instance of the standard built-in Number constructor

    NOTE
        A Number object is created by using the Number constructor in a new expression, supplying a number value as an argument. The resulting object has an internal slot whose value is the number value. A Number object can be coerced to a number value by calling the Number constructor as a function (20.1.1.1).

## 4.3.23 Infinity

number value that is the positive infinite number value

## 4.3.24 NaN

number value that is an IEEE 754-2008 “Not-a-Number” value

## 4.3.25 Symbol value

primitive value that represents a unique, non-String Object property key

## 4.3.26 Symbol type

set of all possible Symbol values

## 4.3.27 Symbol object

member of the Object type that is an instance of the standard built-in Symbol constructor

## 4.3.28 function

member of the Object type that may be invoked as a subroutine

    NOTE
        In addition to its properties, a function contains executable code and state that determine how it behaves when invoked. A function's code may or may not be written in ECMAScript.

## 4.3.29 built-in function

built-in object that is a function

    NOTE
        Examples of built-in functions include parseInt and Math.exp. An implementation may provide implementation-dependent built-in functions that are not described in this specification.

## 4.3.30 property

part of an object that associates a key (either a String value or a Symbol value) and a value

    NOTE
        Depending upon the form of the property the value may be represented either directly as a data value (a primitive value, an object, or a function object) or indirectly by a pair of accessor functions.

## 4.3.31 method

function that is the value of a property

    NOTE
        When a function is called as a method of an object, the object is passed to the function as its this value.

## 4.3.32 built-in method

method that is a built-in function

    NOTE
        Standard built-in methods are defined in this specification, and an ECMAScript implementation may specify and provide other additional built-in methods.

## 4.3.33 attribute

internal value that defines some characteristic of a property

## 4.3.34 own property

property that is directly contained by its object

## 4.3.35 inherited property

property of an object that is not an own property but is a property (either own or inherited) of the object's prototype

# 4.4O rganization of This Specification

The remainder of this specification is organized as follows:

Clause 5 defines the notational conventions used throughout the specification.

Clauses 6-9 define the execution environment within which ECMAScript programs operate.

Clauses 10-16 define the actual ECMAScript programming language including its syntactic encoding and the execution semantics of all language features.

Clauses 17-26 define the ECMAScript standard library. They include the definitions of all of the standard objects that are available for use by ECMAScript programs as they execute.

Clause 27 describes the memory consistency model of accesses on SharedArrayBuffer-backed memory and methods of the Atomics object.

