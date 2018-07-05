---
layout: post
title:  "Intro to modern JS for C/C++ developers, Part 4: Async JS from callbacks to Async/await"
date:   2018-2-24 1:03:00 -0700
categories: tutorials
---
WIP: July 2018

References/Sources:

- [MDN JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Dr Axel Rauschmayer's 2ality blog](http://2ality.com/2018/04/async-iter-nodejs.html)
- [Dr Axel Rauschmayer's ExploringJS site](http://exploringjs.com/)
- [JavaScript Is Sexy](http://javascriptissexy.com/)
- [Zsolt Nagy](http://www.zsoltnagy.eu/es6-iterators-and-generators-in-practice/)
- Wikipedia
- Stack Overflow

# Table of Contents

Section I. Overview
- [About this article](#about-this-article)
- [Higher order functions](#higher-order-functions)
- [Synchronous and Asynchronous](#synchronous-and-asynchronous)
- [Callbacks](#callbacks)

Section 2. The World Turtle
- [Objects](#objects)
- [Symbols](#symbols)
- [Iterators](#iterators)
- [Generators](#generators)
- [Promises](#promises)
- [Async/await](#promises)
- ...

- Other topics of discussion include *async, function pointers, Verilog, hardware clocks, operating systems, Linux*

------
# Section I. Overview
------

# About this article

This article will cover a high level, holistic understanding of asynchronous
JavaScript as simply and accessibly as possible from the perspective of the low
level (C/C++/hardware) developer.

For more insight into functional programming I recommend the  [LambdaCast podcast on SoundCloud](https://soundcloud.com/lambda-cast).

**Infinite regress: turtles all the way down**

The purpose of this article is to [find the "world turtle"](https://en.wikipedia.org/wiki/Turtles_all_the_way_down) of all of these asynchronous concepts in JavaScript.

> **Async/await knowledge relies on Promises + Generators knowledge**
>> **Promises knowledge relies on Generators**
>>> **Generators knowledge relies on Iterators**
>>>> **Iterators knowledge relies on Symbols**
>>>>> **Symbols knowledge relies on Objects**

# Higher order functions

In computing, you'll hear about "higher order functions" a lot.

A higher order function is a function that:
- Returns a function,
- and/or, takes a function as an argument.
- [see here for a debate on these two points.](https://en.wikipedia.org/wiki/Talk%3AHigher-order_function)
- [on Functors](https://en.wikipedia.org/wiki/Talk%3AHigher-order_function#Is_%22higher-order_function%22_synonymous_with_%22functor%22?)

It is often said:

>"In JavaScript, functions are first-class objects."

- In JS, functions are Objects. (*Functions are of the Function data type, which is an Object. [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions)*)
- That means that they can be used like any other Object.
- So, you can do a lot of stuff with functions, including:
- We can pass functions into functions as arguments, and operate on those args.
- We can return functions from functions.

> Callbacks are a great use case for "functions as first class objects" in JS; passing a function as arguments as per above.

> Callbacks are higher order functions.

# Synchronous and Asynchronous

Recall in hardware/combinational logic that we have [synchronous and asynchronous
circuits and hardware such as flip flops and asynchronous latches](http://www.ee.surrey.ac.uk/Projects/CAL/seq-switching/synchronous_and_asynchronous_cir.htm).
If you have hardware and/or Verilog experience, you'll think of "asynchronous" as
"nonblocking", and you'll also likely tend to think of the word "synchronous"
as being related to clocks and duty cycles. As a hardware person, when I hear the word "synchronous" I think about what happens on the very next clock. So, think of
synchronous as meaning, "right away."

**Other fun async things to consider**

It would be fun to talk about things here like threading in Linux and interprocess
communication, or how JavaScript has a single-threaded event loop, but Node is multi-threaded thanks to the C++ worker queue; we could talk about IO-Bound versus CPU-Bound
processes and how Node is similar to Linux/OS development, and all sorts of fun
operating systems concepts and queueing algorithms. Perhaps in a future blog post.
For now, it's great to know that these skills are polymath, transferable skills!

> "**Synchronous:**" occurring at the same time
- [definition](http://www.dictionary.com/browse/synchronous)

> "**Asynchronous** refers to a communication environment where each party receives and processes messages when convenient or possible rather than immediately."
- [MDN]((https://developer.mozilla.org/en-US/docs/Glossary/asynchronous)

# Callbacks

Callbacks are functions which are passed as args to other functions, and then
usually `"called back"` (called within that containing function, either by name,
or by the arguments object which can be used to access lambdas/anonymous functions
passed in as args). [See my first post in this series for more on the arguments object](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html). But usually, you pass them in - and invoke them - by name.

Since they're invoked inside another function, **callbacks are also closures.**

**This and callback context**

[See my first post in this series on "this/bind/call/apply" for more on setting the correct "this."](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html) - if you're using "this" relevant to callbacks, make sure to handle
it correctly for how JavaScript scopes variables.

**Synchronous and Asynchronous Callbacks**

A lot of developers think of callbacks as asynchronous code, and that's true, but
[callbacks can also be synchronous (MDN on callbacks)](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function).

> "A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action." [MDN](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)

> For C/C++ developers: [Callbacks are function pointers in C/C++ (MDN).](https://developer.mozilla.org/en-US/docs/Mozilla/js-ctypes/Using_js-ctypes/Declaring_and_Using_Callbacks)

A lot of the time, callbacks are used when processing time for a network request
is nondeterministic (which is, a lot of the time). Read more about callbacks on
MDN, Dr Axel Rauschmayer's site, JavaScriptIsSexy, or similar.

------
# Section II. The World Turtle
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

console.log(Object.keys(obj))  // [ '2' ] -> it's a string! Not a number.
```

See how the Number 2 was converted into a string? That's because all Object keys
must be a string. [Note the MDN docs on Object keys, which include the "under the
hood" behavior in a polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys). (A polyfill is code that implements newer features on older browsers
that don't support that feature; it's usually conditionally executed).

# Symbols

**Totally Unique**

Use cases:
- Totally unique object keys (don't appear in iterations*; a "hidden layer" in objects)
- Metadata in objects
- Add hooks to objects
- Enums (since values always different)
- Access private properties
- Privacy (see [Information Hiding post](http://blog.amandafalke.com/tutorials/2018/02/23/information-hiding-weakmaps-weaksets.html))
- Symbols don't appear in interations because they're not enumerable; they're not
enumerable because they're not identifiable as normal parts of a "collection"

So, Symbols are pretty weird. They're a primitive type; you can't call "new Symbol."
They're used to identify individual objects, because every time you call a
`Symbol()`, you create a new unique Symbol, with its own `memory reference.`

- *don't have a literal return value.*
- The *return value is a type: symbol, a primitive data type.*
- Pass in a descriptor as arguments: `let aNode = Symbol('identify me')`
- There's also a global Symbol registry. Neat!

```
let q = Symbol()

let obj33 = {
  prop: 'a prop',
  [q]: 'Symbolix'
}

console.log(obj33) // { prop: 'a prop' } ... Symbol doesn't show up!
```

Symbols return the value of Object's .toString() invoked on its own keys.

Recall that JavaScript object keys must always be a string (or Symbol), able to be coerced to a string, or an empty string.

[My Github gist](https://gist.github.com/abstractmachines/18ea0dc6b8b98e307e937806b772f974) on Well Known Symbols, Reflection and Metaprogramming in JS:


<script src="https://gist.github.com/abstractmachines/18ea0dc6b8b98e307e937806b772f974.js"></script>

**There are many well known symbols**

Well Known Symbols are Reflection via implementation in ES6.  
Reflection in Metaprogramming: observing low level internals of a programming
language/program. One example of Well Known Symbols in JS is Symbol.hasInstance, which instanceof uses under the hood.

There are other "well known symbols" that are the implementation, under the hood, of JS functions.

For example, `for of` uses `Symbol.iterator` under the hood (somewhere in the prototype chain, one of the objects in that chain has an @@iterator; each time the `for of` is used, `Symbol.iterator` is invoked). [More info on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator).

Please also see [the gist itself]([link](https://gist.github.com/abstractmachines/18ea0dc6b8b98e307e937806b772f974)) for info on information hiding, why instanceOf sometimes lies, and other nerdy arguments.

References/Sources on Symbols:
- https://www.keithcirkel.co.uk/metaprogramming-in-es6-symbols/
- http://exploringjs.com/es6/ch_symbols.html
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/hasInstance

# Iterators

**We've all iterated**

We've all used `for of`, `.forEach`, `.every`, `.some`, and similar constructs.

In ES6, the ES6 iteration protocol is used under the hood with the spread
operator, with array destructuring, Maps and Sets constructors, Promises,
Array methods, and much more.

> An Iterable Object has an iterator method with the key Symbol.iterator that returns an iterator object: [link](http://www.zsoltnagy.eu/es6-iterators-and-generators-in-practice/)
```
let iterableObject = {
  [Symbol.iterator]() { return iteratorObject; }  
};
```

**Arrays are Iterable**


[My Github gist on Arrays:](https://gist.github.com/abstractmachines/1c72a2bb4dee5b09abebee76fa77c0e0)

<script src="https://gist.github.com/abstractmachines/1c72a2bb4dee5b09abebee76fa77c0e0.js"></script>

More info from [my gist](https://gist.github.com/abstractmachines/1c72a2bb4dee5b09abebee76fa77c0e0):

**Converting other things to arrays**
- Array.from()
- Spread operator can convert Sets to Arrays

- The spread operator inserts the values of an iterable into an Array ([from Dr Axel](http://exploringjs.com/es6/ch_iteration.html)):
```
const arr = ['b', 'c'];
const arr2 = ['a', ...arr, 'd']

// arr2 is now:
> ['a', 'b', 'c', 'd']
```


**Maps and Sets are Iterable, and Plain Objects aren't**

We don't iterate with Objects via "Iterable" protocols because of problems with
the prototype chain, and for [other reasons as well](http://exploringjs.com/es6/ch_iteration.html#sec_plain-objects-not-iterable);
instead, we use `Object.entries()` and `Object.keys()`. In ES6 we also have Maps, Sets:


[My Github gist on Maps and Sets:](https://gist.github.com/abstractmachines/ba21a7f296e6d1726a3a2e845834b2ec)

<script src="https://gist.github.com/abstractmachines/ba21a7f296e6d1726a3a2e845834b2ec.js"></script>

More info from [my gist](https://gist.github.com/abstractmachines/ba21a7f296e6d1726a3a2e845834b2ec):

**For of on Maps (and Sets)**

**Iterating through entire key-value collection with for of**
```
> for ( let pair of artists) { console.log(pair) }

[ 'jazz', 'coltrane' ]
[ 'funk', 'curtis mayfield' ]
```
**Same thing, with specifying key and value for each element**
```
> for ( let [key, value] of artists ) { console.log(key, value) }

jazz coltrane
funk curtis mayfield
```

**Conversions**

You can convert a Set to an Array with the Spread Operator : `let anArray = [...aSet]`



# Generators

WIP

# Promises

WIP

# Async/await

WIP
