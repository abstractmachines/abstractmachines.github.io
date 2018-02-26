---
layout: post
title:  "Introduction to modern JavaScript, Part 3: Information Hiding"
date:   2018-2-23 8:03:00 -0700
categories: tutorials
---
*References:*
- *[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)*
- *[Dr Axel Rauschmeyer](http://2ality.com/2016/01/private-data-classes.html)*
- *[Douglas Crockford](https://crockford.com/javascript/private.html)*
- *David Manning*

# Information Hiding in JavaScript: WeakMaps and WeakSets
"Privacy" in Javascript, aka Encapsulation, if often emulated via Symbol() and similar constructs.

Douglas Crockford says that privacy/privileged methods are possible via `closures` (2001). Things have changed a little since then, but that's a good start.

## 4 different ways to accomplish information hiding
[Rauschmeyer mentions](http://2ality.com/2016/01/private-data-classes.html) 4 different ways to accomplish privacy:

1. **Keeping data private in the class constructor**
2. **Underscore prefix naming conventions for private member variables**
3. **WeakMaps and WeakSets**
4. **Using Symbols as keys for private properties**

Let's discuss these 4 techniques.

### Option 1. Keeping data private in the class constructor
Methods in the constructor are private. These are called `instance methods`. This is very normal for most languages.

`Instance methods` take up extra memory.

`Prototype methods` are "shared".

The constructor method is usually agreed on as one of the better methods. Use let or const to keep it block scoped.
The important part is that the arguments go into the constructor, not the class (similar to C++ copy constructors) in
the JS constructor, and we keep things in the constructor to guarantee privacy and encapsulation.

This article assumes a basic familiarity with C++ programming, the Rule of Three, and OOP. We won't cover it here.

### Option 2. Underscore prefix naming convention for private member variables
This has the drawback of not actually being private. `_stuff`

### Option 3. WeakMaps and WeakSets
The use cases for these structures were not intuitively obvious to me at first.

- **Weakly held references (think about how weak/smart pointers work)**
- **NOT enumerable!!** If they were, you could see their data. (There are, of course, getters).
  ```
  If WeakMaps were enumerable, state would depend on garbage collection. [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
  ```
- **WeakMaps: keys must be Objects.**
- **WeakSets: data types must be Objects.**

Recall that smart pointers are used in modern C++ to allocate temporary ownership.
Weak pointers are a subset of that.

#### On Garbage Collection
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)

Garbage collection uses nondeterminism to intelligently clean up memory.

**Maps**

For the *lifetime* of the Map, references to its keys and values are held indefinitely.
This blocks garbage collection for keys and values of that Map.

Access happens via iteration/enumeration of keys via indexes.

**WeakMaps**

WeakMaps allow deleted elements to be garbage collected; the collection
itself does not indefinitely hold references to its keys. Garbage collection is
nondeterministic. That means that the order of and access to the WeakMap collection is
nondeterministic if based on enumeration/iteration.

**Hence, WeakMaps aren't enumerable.**

This makes them more secure, because accessors must have a specific reference to
that items. WeakMaps can be used in credentials, for example, or

This also makes them better at handling state changes, Promise handling, and
much more.

[This Stack Overflow post covers](https://stackoverflow.com/questions/29413222/what-are-the-actual-uses-of-es6-weakmap)
a lot of what I've been told by senior engineers, which also aligns with my
research.

### Option 4. Using Symbols as keys for private properties
The (somewhat obscure) case for using Symbols for privacy/information hiding [has been made on occasion](https://medium.com/@davidrhyswhite/private-members-in-es6-db1ccd6128a5), but [as Rauschmeyer posits](http://2ality.com/2016/01/private-data-classes.html),
it's not the safest method because properties and Symbols are still enumerable using `Reflect.ownKeys`.



## Conclusion
Use common sense (i.e. constructors) most of the time for encapsulation of private member variables and data in JS.

When required, use WeakMaps and Weaksets.

See you next time,
Amanda
