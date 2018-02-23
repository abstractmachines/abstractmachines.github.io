---
layout: post
title:  "Introduction to modern JavaScript for C/C++ developers, Part 2"
date:   2017-7-10 20:06:20 -0700
categories: tutorials
---
# JavaScript Tutorials for the C/C++ programmer, part 2

Make sure you've read over [Part 1 of this tutorial](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html). Currently, this post "Part 2" is definitively a "work in
progress."

References: MDN, Wikipedia, Eric Elliott Medium blog posts

## Functional programming : deterministic, predictable code
`TODO`: Complete this section.

### Immutability
> There are two ways to change data: update (mutate) existing data, or create a new/updated copy of the data.
>> When we change existing data, side effects (bad things) can occur.

#### Pure Functions
In [Eric Elliot](https://medium.com/@_ericelliott)'s functional programming tutorials on Medium, one of the first things he mentions in his [Pure Functions tutorial](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976) is how objects
are passed into functions in JS **by reference** (and primitives are passed in by value).

If we mutate that object inside of that function, it will mutate the original object *at its original memory location*.
That causes **side effects** which are unknown when we ("we" meaning, flow of execution control) are operating within
the context of the function that brings that object in as a parameter.

## Iterators
With the first step into looking into Iterators in JS, we come across in-depth knowledge of how Symbols work.
>> String, Array, TypedArray, Map and Set are all built-in iterables, because each of their prototype objects implements an @@iterator method. In order to be iterable, an object must implement the @@iterator method, meaning that the object (or one of the objects up its prototype chain) must have a property with a @@iterator key which is available via constant Symbol.iterator... [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

We will leave the discussion of Symbols to [another future blog post](http://blog.amandafalke.com/tutorials/2018/02/23/async-await-infinite-regression.html).

We will also assume that by covering Iterators for Arrays and Objects, that the use cases for Sets, Maps, TypedArray
will seem intuitive. We will leave WeakMaps and WeakSets for a later discussion as well.

### Array iterators

### Use Cases:
- Iterate over an Array and transform its values into an output / use existing values
- Filter to filter out undesired elements
- Map to apply one function to all elements (similar to transform)
- Reduce to accumulate values

***We will begin with the simpler Array iterators and work our way into transducers.***

Note that there are many, many Array.Prototype.DoAThing methods, search the MDN site to learn more.

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
**Returns:** a boolean value … IF all elements meet requirement.

Code is pretty much the same as with `.some`.

### .forEach
Just iterators for collections. Executes a provided function `for each` Array element.

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

### Map
`TODO`: Complete this section.

Map is used to transform an entire data structure.

>> "Pure functions are all about mapping. Functions map input arguments to return values, meaning that for each set of inputs, there exists an output. A function will take the inputs and return the corresponding output."
[“Mapping” — Eric Elliott]( https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)

Since JS map returns "new data" instead of mutating existing data, we can
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

### Reduce
`TODO`: Complete this section.

Reduce **Iterates over a collection and returns an accumulated value.**

### Transducers
`TODO`: Complete this section.

## Object iterators
Before we go into Object iterators, let's talk about how JS objects work.

- Enumerable Properties : does it have its own?
- Iterator Protocols

### Enumerable Properties: "On THIS object, not down the prototype chain"
An Object has Enumerable properties when its properties are its own properties,
instead of just being properties that belong to another object somewhere in the Prototype Chain. Recall that we
covered the Prototype Chain in the [first post of this series](http://blog.amandafalke.com/tutorials/2017/04/12/intro-to-modern-javascript-for-cpp-developers.html).

**There are a lot of methods for Objects in JS on the MDN site. How can I intuitively tell the difference?**

To think simply about whether a method or context refers to the enumerability of an object, think about what it is.

If it's *enumerable*, it *has its own stuff.*

It makes sense now why `hasOwnProperty()` refers to whether an object's property is an enumerable one.

[See more on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties).

### Iterable and Iterator Protocols
Recall that an Object is not "iterable"/"built in iterable", but it does have an Iterator protocol. [MDN](Iterable and Iterator Protocols)

### for of
`for of` and `for in` are iterators for Objects. You can find out more about them on the MDN site.

### for in
[`for in`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in) iterates over the enumerable properties of an Object.

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
