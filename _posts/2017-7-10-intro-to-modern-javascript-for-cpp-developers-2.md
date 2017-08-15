---
layout: post
title:  "Introduction to modern JavaScript for C/C++ developers, Part 2"
date:   2017-7-10 20:06:20 -0700
categories: tutorials
---
# JavaScript Tutorials for the C/C++ programmer, part 2

The information in this article is based solely on information you can get
in the Mozilla JavaScript docs.

Make sure you've read over [Part 1 of this tutorial](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html). Currently, this post "Part 2" is definitively a "work in
progress."

## TODO: Immutability
Discuss why immutability is important

There are two ways to change data: update (mutate) existing variables, or
create a new copy of the data.

TODO


## Map, Filter, Reduce

### Use Cases:
- Filter to filter out undesired elements
- Map to apply one function to all elements (similar to transform)
- Reduce to accumulate values

### Filter
Filters out undesired elements. There's a callback function that
returns a boolean whether or not the element is included in the new set.

Since filter returns "new data" instead of mutating existing data, we can
think of filter as being a safe function to use for immutability.

We will use the indexOf() function, which returns a position within the
selected data structure.

#### Filter example: Return only unique elements (make a Bag into a Set)
If you're familiar with some aspects of Set theory, you'll know that a Set
has only unique elements, and a Bag (or MultiSet) can have repeated elements.
Essentially, this question asks us to create a Set out of a Bag/MultiSet.

(For C++ related examples, recall Standard Library std::set, std::multiset)

**Input: "apple", "pear", "peach", "pear", "apple", "kiwi"**

**Expected Output: "apple", "pear", "peach", "kiwi"**

```
const fruit = ["apple", "pear", "peach", "pear", "apple", "kiwi"]

const aSet = fruit.filter((elem, index, fruity) => fruit.indexOf(elem) === index)

fruitySet
// Result:  ["apple", "pear", "peach", "kiwi"]
```

Note the ES6 arrow function syntax. We could have used ES5:

```
const aSet =
  fruit.filter(function(elem, index, fruity) {
    fruit.indexOf(elem) === index
  })
```
Which is logically equivalent to (and more verbose than):
```
const aSet =
  fruit.filter((elem, index, fruity) => fruit.indexOf(elem) === index)
```


### TODO: Map
Map is used to transform an entire data structure.

Since map returns "new data" instead of mutating existing data, we can
think of filter as being a safe function to use for immutability.

(For C++ related examples, recall Standard Library std::transform)

### Map example: Double all numbers (transform them!)

**Input: 1,2,3,4**
**Expected Output: 2,4,6,8**

```
const nums = [1,2,3,4]

const doubled = nums.map( (nums) => nums*2 )

doubled // result:  [2, 4, 6, 8]
```
Again, we note ES6 arrow function syntax.

### TODO: Reduce
Accumulator

## Every, Some, Each

### .some
`Array.prototype.some`

**Returns:** a boolean value … IF some elements meet requirement.

.some in repl:
```
function isAbove10(elem, index, array) { return elem > 10 }
[1,2,3,4].some(isAbove10) // false
[88,31,2].some(isAbove10) // true
```
```
function isAbove10(elem) { return elem > 10 }
[1,2,3,4].some(isAbove10) // false
[1,2,3,44].some(isAbove10) // true
```

Same thing with arrow functions:
```
const above10 = elem => elem > 10
[1,2,3].some(above10) // false
[10,11].some(above10) // true
```

### .every
`Array.prototype.each`

**Returns:** a boolean value … IF all elements meet requirement.

Code is pretty much the same as with .some.


## TODO: .each ?

## TODO: for of, for in...

## Arrow Functions
Arrow functions resolve the JS `this` problem.

That's accomplished by having "the arrow function's `this` lexically bound
to its enclosing scope."

The "history of `this` (and `that`) in JS" was covered in [Part 1 of this tutorial](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html).

### TODO: More on Arrow Functions

### Arrow Functions: Syntax

TODO
With multiple args: Use parens

```
 x = (y,z) => y*z
```

TODO
With one arg: Parens optional

```
 x = (y) => y*2
```

```
 x = y => y*2
```


TODO
With no args: Parens mandatory

```
 x = () => y*2
```

## TODO: Idiomatic JavaScript

## TODO: Isomorphic JS applications
Rendering JS on server, and hence being able to use same functions

TODO


## TODO: Function Currying
In functional programming, functions aren't "called", they're "applied."
For partial application of functions -- applying only one argument at a time.
Makes things more testable, easier to reason about... TODO

## TODO: More on Call() Apply() and Bind()

### TODO: Bind()
how to use it to partially apply functions, and in function currying

## TODO: Spread Operator

## TODO: ES6 Destructuring