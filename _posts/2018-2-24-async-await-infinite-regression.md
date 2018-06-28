---
layout: post
title:  "Introduction to modern JavaScript, Part 4: Async: From Callbacks To async/await, i.e. the infinite regress post"
date:   2018-2-24 1:03:00 -0700
categories: tutorials
---

# WIP
This is an in progress post.

Last updated : June 2018.

# Topics: Iterators, Generators, Promises, Async/Await

# Motivation
When I began to really get into JS, I heard a lot about Generators, Iterators,
and Async/await. Coming from the hardware world, I've written asynchronous code
in Verilog for logic gate synthesis, shift registers and FPGAs; I've written
"re-entrant" functions in ARM Assembly without an operating system.

Because I have this background and due to my insatiable curiosity for learning
how things work under the hood - combined with the JavaScript universe's penchant
for using buzzwords and "hand waving" rather than describing how things actually
work, I decided to make this blog post.

## Infinite regress: turtles all the way down
The purpose of this article is to [find the "world turtle"](https://en.wikipedia.org/wiki/Turtles_all_the_way_down) of all of these asynchronous concepts in JavaScript which
play a part in understanding how `async/await` - and similar things - really work:


![Propositions](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Infinite_regress_en.svg/298px-Infinite_regress_en.svg.png)

That is to say, we are going to get to the heart of the matter.

**Our world turtle:**

**Async/await relies on Promises**
> **Promises rely on Generators**
>> **Generators rely on Iterators**
>>> **Iterators rely on Symbols**
>>>> **Symbols rely on Objects**
>>>>> **Objects rely on/are the inherent structure of JS**


------
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
