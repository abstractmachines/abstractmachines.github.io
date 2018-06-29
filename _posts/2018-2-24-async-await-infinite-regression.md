---
layout: post
title:  "Intro to modern JS, Part 4: Async JS from callbacks to async/await"
date:   2018-2-24 1:03:00 -0700
categories: tutorials
---
WIP: June 2018

# Table of Contents

- [About this article](#about-this-article)
- [Higher order functions](#higher-order-functions)
- [Callbacks](#callbacks)
- [Objects](#objects)
- [Symbols](#symbols)

# About this article

This article will cover a high level, holistic understanding of asynchronous
JavaScript as simply and accessibly as possible. Code examples are in vanilla JS.

For more insight into functional programming I recommend the  [LambdaCast podcast on SoundCloud](https://soundcloud.com/lambda-cast).

## Infinite regress: turtles all the way down

The purpose of this article is to [find the "world turtle"](https://en.wikipedia.org/wiki/Turtles_all_the_way_down) of all of these asynchronous concepts in JavaScript.

> **Async/await knowledge relies on Promises + Generators knowledge**
>> **Promises knowledge relies on Generators**
>>> **Generators knowledge relies on Iterators**
>>>> **Iterators knowledge relies on Symbols**
>>>>> **Symbols knowledge relies on Objects**

On infinite regress and the "world turtle":

![Propositions](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Infinite_regress_en.svg/298px-Infinite_regress_en.svg.png)

------
# Higher order functions

In computing, you'll hear about "higher order functions" a lot.

A higher order function is a function that:
- Returns a function,
- and/or, takes a function as an argument.
- [see here for a debate on these two points.](https://en.wikipedia.org/wiki/Talk%3AHigher-order_function)
- [I am not going to talk about Functors right now.](https://en.wikipedia.org/wiki/Talk%3AHigher-order_function#Is_%22higher-order_function%22_synonymous_with_%22functor%22?)

Ever hear this?

>"In JavaScript, functions are first-class objects."

Here's what that means.

- In JS, functions are Objects. (*Functions are of the Function data type, which is an Object. [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions)*)
- That means that they can be used like any other Object.
- So, you can do a lot of stuff with functions, including:
- We can pass functions into functions as arguments, and operate on those args.
- We can return functions from functions.
- That's it. That's what that sentence `functions are first-class objects in JS` means.

> Callbacks are a great use case for "functions as first class objects" in JS; passing a function as arguments as per above.

> Callbacks are higher order functions.

------
# Callbacks

> "A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action." [MDN](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)

> For C/C++ developers: [Callbacks are function pointers in C/C++ (MDN).](https://developer.mozilla.org/en-US/docs/Mozilla/js-ctypes/Using_js-ctypes/Declaring_and_Using_Callbacks)

## Synchronous Callbacks

```
function sayName(name) { return console.log(name) }

function greet(name, callback) {
  console.log('Hello')

  callback(name)
}

greet('World', sayName)
```

------
# Objects

It's relative at this point to bring up a few review topics about JS objects:
- Object property keys must be Strings (or Symbols), so they're coerced using `.toString()`
- Square bracket notation can be used to access properties (keys) via expression assignment

```
const obj = {}

const key = 2

obj[key] = 'yarp'                 

console.log(obj)               // { 2: 'yarp' }

console.log(typeof key)       // number!
console.log(typeof obj[key]) // string! It got coerced

console.log(Object.getOwnPropertySymbols(obj))
```
*typeof and instanceof are not recommended for type detection in JS*

WIP

------
# Symbols

So, Symbols are pretty weird. They *don't have a literal return value.* The *return value is a type: symbol.*

Symbols return the value of Object's .toString() invoked on its own keys.

Recall that JavaScript object keys must always be a string (or Symbol), able to be coerced to a string, or an empty string.

WIP
