---
layout: post
title:  "Intro to modern JS for C/C++ developers, Part 4: Async JS from callbacks to Async/await"
date:   2018-2-24 1:03:00 -0700
categories: tutorials
---

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
- [JS is single threaded and uses async queues](#js-is-single-threaded-and-uses-async-queues)
- [Callbacks](#callbacks)

Section 2. The World Turtle
- [Objects](#objects)
- [Symbols](#symbols)
- [Iterators](#iterators)
- [Generators](#generators)
- [Promises](#promises)
- [Coroutines](#coroutines)
- [Async/await](#async/await)
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

# JS is single threaded and uses async queues

Recall in Verilog and/or hardware/combinational logic that we have [synchronous and asynchronous
circuits and hardware such as flip flops and asynchronous latches](http://www.ee.surrey.ac.uk/Projects/CAL/seq-switching/synchronous_and_asynchronous_cir.htm);

Recall also that C languages are generally synchronous (pthread excepted); that C++
has std::thread, and that we use things in assembly code to pipeline and take into
account particular parts of the shift register pipeline of the CPU.

- Synchronous: "line 2 cannot execute until line 1 is complete."
- Asynchronous: "Whatever is available and convenient, executes now."

Ever hear this: "JavaScript is single threaded .... AND async." Well, that's not
exactly correct. The JS engine uses a call stack... *and it uses multiple queues
to simulate asynchronous operations.*
```
function first() {
    setTimeout(function(){
        console.log(1)
    }, 500)
}

function second() {
    console.log(2)
}
```

```
first()
second()

> 2
> 1
```

Wow - that's cool. Is JS really a single threaded language?

Answer: It absolutely is. It just "emulates" asynchronous behavior via the use of
multiple queues which enqueue events, and fire those events on the call stack *when
the scheduler for those events has decided to push those events on the call stack.*


The concepts above are similar to how Node (V8 Engine) uses a C++ worker queue threadpool
for jobs that have a different processing time (see operating systems and "CPU-Bound"
versus "IO-Bound" jobs).

[Read this for more information on the queues and stacks
JS uses to emulate an asynchronous language](https://medium.com/@siddharthac6/javascript-execution-of-synchronous-and-asynchronous-codes-40f3a199e687)

Callbacks are one of those asynchronous constructs which are placed on a queue when
defined, and which the queue places on the call stack when the scheduler has determined
that it's time to execute.

# Callbacks

Callbacks are functions which are passed as args to other functions, and then
usually `"called back"` (called within that containing function, either by name,
or by the arguments object which can be used to access lambdas/anonymous functions
passed in as args). [See my first post in this series for more on the arguments object](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html). But usually, you pass them in - and invoke them - by name.

Since they're invoked inside another function, **callbacks are also closures.**

**This and callback context**

[See my first post in this series on "this/bind/call/apply" for more on setting the correct "this."](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html) - if you're using "this" relevant to callbacks, make sure to handle
it correctly for how JavaScript scopes variables.

See also:

[MDN on callbacks)](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function).

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

### All Object keys must be strings
See how the Number 2 was converted into a string? That's because all Object keys
must be a string. `.toString()` is called under the hood to convert!

[Note the MDN polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys).

# Symbols

**Totally Unique**

Recall that JavaScript object keys must always be a string (or Symbol), able to be coerced to a string, or an empty string.

So, Symbols are pretty weird. They're a primitive type; you can't call "new Symbol",
because the `new` keyword is for Objects.

Every time you call `Symbol()`, you create a new unique Symbol, with its own `memory reference.`

Use cases:
- Totally unique object keys (don't appear in iterations*; a "hidden layer" in objects) ... avoid name collisions, use globals without fear
- Metadata in objects
- Add hooks to objects
- Enums (since values always different)
- Privacy (see [Information Hiding post](http://blog.amandafalke.com/tutorials/2018/02/23/information-hiding-weakmaps-weaksets.html))

```
let q = Symbol()

let obj33 = {
  prop: 'a prop',
  [q]: 'Symbolix'
}

console.log(obj33) // { prop: 'a prop' } ... Symbol doesn't show up!
```

[My Github gist](https://gist.github.com/abstractmachines/18ea0dc6b8b98e307e937806b772f974) on Well Known Symbols, Reflection and Metaprogramming in JS:

<script src="https://gist.github.com/abstractmachines/18ea0dc6b8b98e307e937806b772f974.js"></script>

Please also see [the gist itself]([link](https://gist.github.com/abstractmachines/18ea0dc6b8b98e307e937806b772f974)) for info on information hiding, why instanceOf sometimes lies, and other nerdy arguments.


# Iterators

**We've all iterated**

We've all used `for of`, `.forEach`, `.every`, `.some`, and similar constructs.

In ES6, the ES6 iteration protocol is used under the hood with the spread
operator, with array destructuring, Maps and Sets constructors, Promises,
Array methods, and much more.

### Iterator Object

> An Iterator Object has a `next()` method which returns `done` and `value`,
and it iterates until done is truthy:  [source](http://www.zsoltnagy.eu/es6-iterators-and-generators-in-practice/)
```
let iteratorObject = {
    next() {
        return {
            done: true,
            value: null
        };
    }    
};
```

### Iterable Object returns Iterator Object via Symbol.iterator

> An Iterable Object has an iterator method with the key Symbol.iterator that returns an iterator object: [source](http://www.zsoltnagy.eu/es6-iterators-and-generators-in-practice/)
```
let iterableObject = {
  [Symbol.iterator]() { return iteratorObject; }  
};
```

### Arrays are Iterable


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


### Maps and Sets are Iterable, and Plain Objects aren't

We don't iterate with Objects via "Iterable" protocols because of problems with
the prototype chain, and for [other reasons as well](http://exploringjs.com/es6/ch_iteration.html#sec_plain-objects-not-iterable);
instead, we use `Object.entries()` and `Object.keys()`. In ES6 we also have Maps, Sets:


[My Github gist on Maps and Sets:](https://gist.github.com/abstractmachines/ba21a7f296e6d1726a3a2e845834b2ec)

<script src="https://gist.github.com/abstractmachines/ba21a7f296e6d1726a3a2e845834b2ec.js"></script>

More info from [my gist](https://gist.github.com/abstractmachines/ba21a7f296e6d1726a3a2e845834b2ec):

#### For of on Maps (and Sets)

**Iterating through entire key-value collection with for of**
```
> for ( let pair of artists) { console.log(pair) }

[ 'jazz', 'coltrane' ]
[ 'funk', 'curtis mayfield' ]
```
#### Same thing, with specifying key and value for each element
```
> for ( let [key, value] of artists ) { console.log(key, value) }

jazz coltrane
funk curtis mayfield
```

#### Conversions

You can convert a Set to an Array with the Spread Operator : `let anArray = [...aSet]`


### Iterators: Strings, spread, and for of

[Another little gist I made:](https://gist.github.com/abstractmachines/fb010c0385054237f8b04ae158aed873)

<script src="https://gist.github.com/abstractmachines/fb010c0385054237f8b04ae158aed873.js"></script>

# Generators

Use cases:
- Lazy evaluation : only evaluate parts of a function when necessary
- Processing large data sets / lots of memory
- Asynchronous programming

The function signature of a Generator function uses a pointer:

```
let gen = function * generateRadness () {
  // yield something
}

let callGen = gen()

gen.next() // return something that was yielded.

// goto back to Generator.... return (default undefined).
```
### Generators are reentrant

Most people who have written Assembly have hand-written reentrant functions,
without an operating system. I've done this, and it has immensely helped me
to understand what all of this is about.

You can think of Generators as reentrant functions ([description here](https://en.wikipedia.org/wiki/Reentrancy_(computing))) that are "interrupted"
and `yield` control. The interrupt is initiated by the `next()` invocation
used by a reference to the Generator; when `next()` is completed, control flow
is returned to  the Generator in the next line of code down. It's similar to a
`goto` statement. A lot of high level developers think that `goto` statements are
always bad - but the Linux kernel has thousands of these statements.

- Generator functions return a generator object that's both Iterable AND Iterator,
so they have a `Symbol.iterator` method for the traversal/pointer, AND a `.next()`.

- `yield`: pauses execution (in the Generator)

- `next()`: resumes execution (from the caller manipulating the Generator)

### Generators implement iterables
A generator function returns a generator object which is iterable, and hence may
be consumed by iterable constructs (`for of`, etc).

```
  function * generateRadness () {
     yield 'rad'
     yield 'radder'
     yield 'raddest'
  }

  let iterateRadness = generateRadness()

  iterateRadness.next() // {value: "rad", done: false}
  iterateRadness.next() // {value: "radder", done: false}
  iterateRadness.next() // {value: "raddest", done: false}
  iterateRadness.next() // {value: undefined, done: true}
```

We know iterables can be consumed with iterator constructors. Same for Generators.
Let's use some ES6 spreading:

```
  let destructureRadness = generateRadness()

  [ ... destructureRadness ] // (3) ["rad", "radder", "raddest"]
```
Where are we now? We're at the end! Pointers, people!
```
  destructureRadness.next()  // {value: undefined, done: true}
```

Generators return undefined by default. Let's make a custom return.


```
  function * returnToForever () {
     yield 'chickCorea'
     yield 'is the best'
     yield 'shredder'
     return 'to forever'
  }

  let iterator = returnToForever()

  iterator.next() // {value: "chickCorea", done: false}
  iterator.next() // {value: "is the best", done: false}
  iterator.next() // {value: "shredder", done: false}
  iterator.next() // {value: "to forever", done: true}
```
You can do all sorts of stuff with Generators. Here's an up counter.

```
  let getCountupIterator = function *() {
    let i = 0;
    while (i <= 10) {
      yield i++;
    }
  }
```

It's iterable, so we can spread it:
```
  console.log ( [ ...getCountupIterator() ] // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### Consuming (and controlling) a Generator with an Iterator

Or we can just create an iterator, and then call next:
```
  let it = getCountupIterator()

  it.next() // {value: 0, done: false}
  .....
  .....
  it.next() // {value: 10, done: false}
  it.next() // {value: undefined, done: true}
```

You can also use arguments inside the caller to feed new values to the Generator
which are then used by the Generator in that new context when control flow
returns to the Generator after .next() completes.

**Generators Conclusion**

There's a lot you can do here with Generators that I haven't covered. Look it up!

# Promises

[Promises: MDN](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Promise.jsm/Promise)

[Promises: Fundamentals (Google Developers)](https://developers.google.com/web/fundamentals/primers/promises)

[Promises: States and Fates](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md) is a great document on Promises written by a Google developer Domenic Denicola.

From that article, let's discuss Promise States and Promise Fates:

States:
- Fulfilled (settled)
- Rejected (settled)
- Pending

Fates:
- Resolved
- Unresolved

*(A promise is Resolved if resolution operations have no effect (because it's either
already settled, or it's chained into another promise).*

Promise chains are great because they simulate synchronous code by catching all
Exceptions. If you throw an error, you can .catch() it later. [See MDN: Using Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

Most of the time we write Promises from scratch, we use them to wrap old APIs [see MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises):

```
setTimeout(() => saySomething("10 seconds passed"), 10000);
```

Let's wrap that in a Promise:
```
const wait = ms => new Promise(resolve => setTimeout(resolve, ms));

wait(10000).then(() => saySomething("10 seconds")).catch(failureCallback);
```

# Coroutines
See:
http://2ality.com/2015/03/no-promises.html

https://medium.freecodecamp.org/write-modern-asynchronous-javascript-using-promises-generators-and-coroutines-5fa9fe62cf74

# Async/await

async/await works a lot like Generators:
- `await` keyword replaces `yield`.
- Instead of function*, it uses the async function keyword.
https://medium.freecodecamp.org/some-of-javascripts-most-useful-features-can-be-tricky-let-me-explain-them-4003d7bbed32

Know what'd be cool? If Generators could return a promise instead of just a single value, and the external function that controls the .next() of the Generator, would handle the Promise chain until the Generator finally returns. Well, that's actually the case - we have libraries to do that. Then the Generator still needs an external "controlling" function, like the Iterator above that consumes the Generator as it produces.

However, with async/await, that's built in! AND we don't need an external function for it.

```
  async function doStuff() {
    const results = doAsyncXHRStuff() // yields, waits!
    return results
  }
```

*Caveats:*
- Async/await can only execute sequentially, not in parallel, so choose the use cases wisely;
- Async/await always returns a Promise.

See:
[https://medium.com/front-end-hacking/modern-javascript-and-asynchronous-programming-generators-yield-vs-async-await-550275cbe433](https://medium.com/front-end-hacking/modern-javascript-and-asynchronous-programming-generators-yield-vs-async-await-550275cbe433)

See also:
[https://hackernoon.com/async-await-generators-promises-51f1a6ceede2](https://hackernoon.com/async-await-generators-promises-51f1a6ceede2)

[http://www.zsoltnagy.eu/a-practical-introduction-to-es2017-async-await/](http://www.zsoltnagy.eu/a-practical-introduction-to-es2017-async-await/
)
