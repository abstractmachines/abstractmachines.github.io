---
layout: post
title:  "Introduction to modern JavaScript, Part 4: Async: From Callbacks To async/await, i.e. the infinite regress post"
date:   2018-2-24 1:03:00 -0700
categories: tutorials
---

# WIP
This is an in progress post.

Last updated : June 2018.

## The point of this post is to understand/teach async/await

To do that properly, we must understand what async/await is built upon. *To understand one thing, you must understand other things.*

## Infinite regress: turtles all the way down

The purpose of this article is to [find the "world turtle"](https://en.wikipedia.org/wiki/Turtles_all_the_way_down) of all of these asynchronous concepts in JavaScript which
play a part in understanding how `async/await` - and similar things - really work:

![Propositions](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Infinite_regress_en.svg/298px-Infinite_regress_en.svg.png)

That is to say, we are going to get to the heart of the matter.

**Our world turtle:**

> **Async/await knowledge relies on Promises + Generators knowledge**
>> **Promises knowledge relies on Generators**
>>> **Generators knowledge relies on Iterators**
>>>> **Iterators knowledge relies on Symbols**
>>>>> **Symbols knowledge relies on Objects**

------
## Higher order functions

In computing, you'll hear about "higher order functions" a lot. What's that mean? Well ... it's debated, but in general:

A higher order function is a function that:
- Returns a function.
- and/or, takes a function as an argument.
- [see here for a debate on these two points.](https://en.wikipedia.org/wiki/Talk%3AHigher-order_function)
- So it's a function dealing with functions.
- [I am not going to talk about Functors right now.](https://en.wikipedia.org/wiki/Talk%3AHigher-order_function#Is_%22higher-order_function%22_synonymous_with_%22functor%22?)

> Note: as per usual, passing functions into functions, and/or returning functions, isn't something we see only in JS, but as per usual, JS developers have their own language and universe for describing it. If you're an interested C/C++ developer, look up "function pointers." Even [MDN mentions that](https://developer.mozilla.org/en-US/docs/Mozilla/js-ctypes/Using_js-ctypes/Declaring_and_Using_Callbacks) callbacks are function pointers in C.

Ever hear this?

>"In JavaScript, functions are first-class objects."

Here's what that means.

- In JS, functions are Objects. (*Functions are of the Function data type, which is an Object. [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions)*)
- That means that they can be used like any other Object.
- So, you can do a lot of stuff with functions, including:
- We can pass functions into functions as arguments, and operate on those args.
- We can return functions from functions.
- That's it. That's what that sentence `functions are first-class objects in JS` means.

> Callbacks are a great use case for "functions as first class objects" in JS.

## Callbacks

WIP

## Objects

It's relative at this point to bring up a few review topics about JS objects:
- Object property keys must be Strings (or Symbols), so they're coerced using `.toString()`
- Square bracket notation can be used to access properties (keys) via expression assignment

```
const obj = {}

const key= 2

obj[key] = 'yarp'                 

console.log(obj)               // { 2: 'yarp' }

console.log(typeof key)       // number!
console.log(typeof obj[key]) // string! It got coerced

console.log(Object.getOwnPropertySymbols(obj))
```
*typeof and instanceof are not recommended for type detection in JS*


### Symbols: return value of Object's .toString() invoked on its own keys
So, Symbols are pretty weird. They *don't have a literal return value.* The *return value is a type: symbol.*

Recall that JavaScript object keys must always be a string (or Symbol), able to be coerced to a string, or an empty string.

WIP, To Be Continued....
