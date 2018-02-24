---
layout: post
title:  "Introduction to modern JavaScript, Part 3: Information Hiding"
date:   2018-2-23 8:03:00 -0700
categories: tutorials
---
# Information Hiding in JavaScript: WeakMaps and WeakSets
"Privacy" in Javascript, aka Encapsulation, if often emulated via Symbol() and similar constructs.

References:
- Douglas Crockford site
- Dr Axel Rauschmeyer site
- MDN
- David Manning

[Douglas Crockford](https://crockford.com/javascript/private.html) says that privacy/privileged methods are possible via `closures` (2001). Things have changed a little since then, but that's a good start.

## 4 different ways to accomplish information hiding
[Axel Rauschmayer mentions](http://2ality.com/2016/01/private-data-classes.html) 4 different ways to accomplish privacy:

1. **Keeping data private in the class constructor**
  Methods in the constructor are private. These are called `instance methods`. This is very normal for most languages.

  `Instance methods` take up extra memory.

  `Prototype methods` are "shared".

  The constructor method is usually agreed on as one of the better methods. Use let or const to keep it block scoped.
  The important part is that the arguments go into the constructor, not the class (similar to C++ copy constructors) in
  the JS constructor, and we keep things in the constructor to guarantee privacy and encapsulation.

  This article assumes a basic familiarity with C++ programming, the Rule of Three, and OOP. We won't cover it here.

2.  **Underscore prefix naming conventions for private member variables**

  This has the drawback of not actually being private. `_stuff`

3. **WeakMaps and WeakSets**
  This article discusses WeakMaps and WeakSets.

  Using WeakMaps and WeakSets is a good way to encapsulate data as well.

4. **Using Symbols as keys for private properties**

  Symbols are a pretty obscure technique.

## WeakMaps and WeakSets : similar to Weak Pointers
Recall that smart pointers are used in modern C++ to allocate temporary ownership. Weak pointers are a subset of that.

### On Garbage Collection
With regular maps and sets, there is "no" garbage collection for recently deleted items. The deleted items are GC'ed
after the collection itself is deleted.

WeakMaps/WeakSets are JavaScript's answer to the dangling pointer/reference problem - they allow deleted elements to be garbage collected.

### Weakly Held References and Dangling Pointer Solutions

- **WeakMaps: keys must be Objects.**
- **WeakSets: data types must be Objects.**
- WeakMaps/WeakSets have **weakly held references, just like a weak pointer.**
- WeakMaps/WeakSets are NOT enumerable!! If they were, you could see their data. (There are, of course, getters).

WeakMaps are used commonly in JS for information hiding. They were used in Firefox Developer Tools. They are used in
credentials.

## Conclusion
Use common sense (i.e. constructors) most of the time for encapsulation of private member variables and data in JS.

When required, use WeakMaps and Weaksets.

See you next time,
Amanda
