---
layout: typevalue
title: EMCAScript的数据类型和值
date: 2019-01-21 00:20:40
tags:
---
Algorithms within this specification manipulate values each of which has an associated type. The possible value types are exactly those defined in this clause. Types are further subclassified into ECMAScript language types and specification types.

Within this specification, the notation “Type(x)” is used as shorthand for “the type of x” where “type” refers to the ECMAScript language and specification types defined in this clause. When the term “empty” is used as if it was naming a value, it is equivalent to saying “no value of any type”.

# 6.1 ECMAScript Language Types

An ECMAScript language type corresponds to values that are directly manipulated by an ECMAScript programmer using the ECMAScript language. The ECMAScript language types are Undefined, Null, Boolean, String, Symbol, Number, and Object. An ECMAScript language value is a value that is characterized by an ECMAScript language type.

## 6.1.1 The Undefined Type

The Undefined type has exactly one value, called undefined. Any variable that has not been assigned a value has the value undefined.

## 6.1.2 The Null Type

The Null type has exactly one value, called null.

## 6.1.3 The Boolean Type

The Boolean type represents a logical entity having two values, called true and false.

## 6.1.4 The String Type

The String type is the set of all ordered sequences of zero or more 16-bit unsigned integer values (“elements”) up to a maximum length of 253 - 1 elements. The String type is generally used to represent textual data in a running ECMAScript program, in which case each element in the String is treated as a UTF-16 code unit value. Each element is regarded as occupying a position within the sequence. These positions are indexed with nonnegative integers. The first element (if any) is at index 0, the next element (if any) at index 1, and so on. The length of a String is the number of elements (i.e., 16-bit values) within it. The empty String has length zero and therefore contains no elements.

ECMAScript operations that do not interpret String contents apply no further semantics. Operations that do interpret String values treat each element as a single UTF-16 code unit. However, ECMAScript does not restrict the value of or relationships between these code units, so operations that further interpret String contents as sequences of Unicode code points encoded in UTF-16 must account for ill-formed subsequences. Such operations apply special treatment to every code unit with a numeric value in the inclusive range 0xD800 to 0xDBFF (defined by the Unicode Standard as a leading surrogate, or more formally as a high-surrogate code unit) and every code unit with a numeric value in the inclusive range 0xDC00 to 0xDFFF (defined as a trailing surrogate, or more formally as a low-surrogate code unit) using the following rules:

    · A code unit that is not a leading surrogate and not a trailing surrogate is interpreted as a code point with the same value.

    · A sequence of two code units, where the first code unit c1 is a leading surrogate and the second code unit c2 a trailing surrogate, is a surrogate pair and is interpreted as a code point with the value (c1 - 0xD800) × 0x400 + (c2 - 0xDC00) + 0x10000. (See 10.1.2)

    · A code unit that is a leading surrogate or trailing surrogate, but is not part of a surrogate pair, is interpreted as a code point with the same value.

The function String.prototype.normalize (see 21.1.3.12) can be used to explicitly normalize a String value.  String.prototype.localeCompare (see 21.1.3.10) internally normalizes String values, but no other operations implicitly normalize the strings upon which they operate. Only operations that are explicitly specified to be language or locale sensitive produce language-sensitive results.

    NOTE
    The rationale behind this design was to keep the implementation of Strings as simple and high-performing as possible. If ECMAScript source text is in Normalized Form C, string literals are guaranteed to also be normalized, as long as they do not contain any Unicode escape sequences.

In this specification, the phrase "the string-concatenation of A, B, ..." (where each argument is a String value, a code unit, or a sequence of code units) denotes the String value whose sequence of code units is the concatenation of the code units (in order) of each of the arguments (in order).

## 6.1.5 The Symbol Type
The Symbol type is the set of all non-String values that may be used as the key of an Object property (6.1.7).

Each possible Symbol value is unique and immutable.

Each Symbol value immutably holds an associated value called [[Description]] that is either undefined or a String value.

### 6.1.5.1 Well-Known Symbols
Well-known symbols are built-in Symbol values that are explicitly referenced by algorithms of this specification. They are typically used as the keys of properties whose values serve as extension points of a specification algorithm. Unless otherwise specified, well-known symbols values are shared by all realms (8.2).

Within this specification a well-known symbol is referred to by using a notation of the form @@name, where “name” is one of the values listed in Table 1.

<center>Table 1: Well-known Symbols</center>

| Specification Name | [[Description]] | Value and Purpose |
| :------: | :------: | :------ |
| @@asyncIterator | "Symbol.asyncIterator" | A method that returns the default AsyncIterator for an object. Called by the semantics of the  for-await-of statement. |
| @@hasInstance | 	"Symbol.hasInstance" | A method that determines if a constructor object recognizes an object as one of the constructor's instances. Called by the semantics of the  instanceof operator. |