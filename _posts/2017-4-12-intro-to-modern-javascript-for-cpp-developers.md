---
layout: post
title:  "Introduction to modern JavaScript for C/C++ developers, part 1"
date:   2017-4-12 12:50:20 -0700
categories: tutorials
---
# JavaScript Tutorials for the C/C++ programmer or CS Grad, Part 1

This is a "bird's eye" tutorial of JS which is very broad in scope. The purpose of
this document is to introduce C/C++ developers and/or CS grads to concepts in JavaScript.

As such, it is an overly long article, and should be used as as a reference for
basic information.

Not covered in this tutorial: Extensive Mozilla DOM API, JS libraries and frameworks, contextual implementation details.

This is the first tutorial in a series of similar tutorials I'll be posting.

# Sources

This was written by me, with information gleaned from:

1. [Mozilla developer docs](https://developer.mozilla.org/en-US/)
2. Wikipedia
3. [Design Patterns by GoF](https://en.wikipedia.org/wiki/Design_Patterns)
4. Stack Overflow
5. [Dr Rauschmeyer's JavaScript blog](http://www.2ality.com/)
6. [JavaScript Is Sexy](http://javascriptissexy.com/)

# Table of Contents

- [Typing](#typing)
- [Strict Mode](#strict-mode)
- [Objects and Primitives](#objects-and-primitives)
- [On JS Inheritance with Objects and the Prototype Chain](#on-js-inheritance-with-objects-and-the-prototype-chain)
- [Bitwise and Memberwise: Shallow Clone and Deep Clone](#shallow-clone-and-deep-clone)
- [Object Enumerable Properties](#object-enumerable-properties)
- [Object Iteration](#object-iteration)
- [JS Operators](#js-operators)
- [JS Functions](#js-functions)
- [Apply Call And Bind](#apply-call-and-bind)
- [ES6 Arrow Functions](#arrow-functions)
- [Const, let, and var](#const-let-var)
- [ES6 Iterators Implicit and Explicit](#es6-iterators-implicit-and-explicit)
- [Common JavaScript Design Patterns](#common-javascript-design-patterns)

## Typing
"Some languages, like Java and C++, have **static typing**.
Java is generally supposed to have **strong (and static) typing.**
C/C++ are statically typed languages with weak typing."

... What's this all mean?

Several computer science topics were just covered in the prior paragraph.
We'll cover them briefly below.

*Related Topics:*
+ Type Safety
+ Static Typing, Dynamic Typing
+ Type Checking (Strong and Weak)
+ Type Inference; Type Erasure
+ Type Conversions (any kind of type conversion),
+ Type Coercion
+ Implicit type conversions (little type safety),
+ Casting (explicit type conversions),
+ Compiled versus interpreted languages,
+ Other stuff.

### Type Safety
**Type safety** is the relative amount of type-related error prevention a
language has built in. The more type-related error prevention that a language
has, and/or the harder a language's typing system is to subvert, means that the
typing system definition for that language will lean more towards the "strongly
typed" category; similarly, the easier a language's typing system is to subvert,
the more that a language will lean towards the "weakly typed" category.

**Lots of type safety: Type conversions generally must be explicit**

Language tends do discourage and/or prevent type errors.

**Little type safety: Implicit conversions can happen fairly easily**

Language tends to implicitly allow type errors, often silently.

**What's best? It depends.**

Not having type safety can be a feature. Consider "truthy" languages like JS
and other scripting languages.
See also: [Duck typing](https://en.wikipedia.org/wiki/Duck_typing)


**Related terminology:**

`data type` ... `size` ... `offset`

`memory safety (RAM access problems including buffer overflows and dangling pointers)`

### Type enforcement: Static or Dynamic (compile time check or runtime)

`Static Type Enforcement:` at compile time

`Dynamic Type Enforcement:` at run time

> **The term "memory-safe languages" does not include C/C++.**
The use of reference counters, understanding of strong v. weak pointers,
C++11's smart pointers, semaphores, reerence counting et al, are all tools
the modern C/C++programmer uses to prevent memory problems. This level of
consideration doesn't even enter the mind of the programmer writing higher
level languages. Many higher level languages disallow pointer arithmetic and
casting to be accessed programatically, and hence those languages solve the
memory safety problem (sometimes at a performance cost particularly for RTOS).

**The terms "memory-safe languages" "static typed language" and "dynamic typed
language" refers to the type enforcement that the language uses.**
If a language's type enforcement strategy is static, then it is called a
statically typed language. And so on and so forth.

**Static typed languages (Java, C, C++)** determine type correctness at compile
time. Even if you don't know this term already, you should be able to intuit its
 meaning as a software engineer, namely due to the use of the term **static.**
 Recall that **static** means **at compile time** and **dynamic** means
 **at runtime.**

**Dynamically typed languages (Ruby, Python, PHP)** determine type correctness
at runtime, or execution time. As C programmers, we've heard runtime typing
referred to as "late" or "latent typing", and many proponents of statically
typed languages state that to check for types so late is expensive. There are
many arguments against and for, but the purpose of this document is a tutorial,
not a nerd battle (fun as they can be).

**Strongly typed vs. weakly typed**

**If a type system is easy to subvert,** then the language is considered
**weakly typed**.

If a **variable remains a specific type unless explicitly reassigned to another
type, then the language is considered **strongly typed.**

Think about **implicit type conversions in C/C++.** Doesn't that mean that the
type system is hence easy to subvert? Clearly. Therefore, C and C++ can be
considered weakly typed languages.

Think about **casting in C/C++.**

In C/C++, any type can literally become another type. Void pointers...implicit
type conversions... **"C/C++ is therefore weakly typed because its typing system
is easy to subvert."**

In languages such as Java and C#, there are **no/fewer implicit conversions**
of types, because **you have to explicitly convert types.** These languages are
therefore known as **strongly typed, because the type system is more difficult
to subvert, and the programmer must generally explicitly do so.**

The terms **"strongly typed" (Java, Python)** and "weakly typed" can refer to
either statically typed languages or dynamically typed languages, and seem
controversial/debatable.

 Strongly typed seems to mean **"variables must be declared before use."**  
 It could also mean **"there are rules about types in this language, and those
 rules are hard to break."** There must be a predetermined memory
 offset/allocation for each data type built in as a primitive or used-defined
 types.

> JavaScript is untyped; JavaScript is PROTO-typed.

>"academic types use "untyped" to mean "no static types".
they are smart enough to see that values have types (duh!). context matters."
-Brendan Eich
https://twitter.com/brendaneich/status/166310376340848643

**"Untyped languages"** can also be used to refer to **"dynamically typed
languages"**, and that is often the case in the CS world:

http://stackoverflow.com/questions/9154388/does-untyped-also-mean-dynamically-typed-in-the-academic-cs-world

## Strict Mode

> Strict Mode: less "undefined", more checking

JavaScript semantics, as we know them, often involve a silent or implicit
undefined value getting by us. This, as you may imagine, can be problematic.

Strict mode is a solution for these problematic semantics. Strict mode converts
that silent error into an explicit throw error.

Strict mode  does more "checking". Strict mode code can also be faster.

Strict mode `function calls` are different, too. In JS semantics as we know them,
function calls have a `this` value of the `global object` by default. In strict
mode, `it is undefined!!!!`

# Objects and Primitives

## JavaScript Primitives (values)
Anything that's a primitive type such as boolean, number, string, null,
undefined, and in ES6, Symbol.

These primitives have "wrappers" that translate values to primitive types.
Those wrappers are called Boolean, String, Number, and in ES6, Symbol.

## Anything that isn't a Primitive is considered an Object.

## Primitives Are Passed by Value, Objects passed By Implicit Reference (kinda):
Primitives (data types) are passed by value into functions, but any soft of
Object is passed by "reference," implicitly. Passing an Object (a non primitive
inherent to JS which inherits from Number, Function, Object etc, or a
user-created Object) results in changes available outside the function's scope.

## Values:
1. Immutable
2. Evaluated by value (equality/comparison operators `===`)

## Objects:
1. Mutable by default
2. Evaluated by reference (equality/comparison operators `===`)
3. Since JavaScript has Prototype Inheritance, and the Prototype Chain, the
only construct existing in JS "inheritance" is the Object and Prototype Chain
4. Have methods such as `isEnumerable` and `hasOwnPrototype` to peek at either object itself or objects in Prototype Chain
5. Passed into functions by reference, causing unwanted side effects (more on this later)

**What is an Object?**
Anything that doesn't use the built-in primitives. JavaScript is fairly object-centric; its inheritance model is
based upon Object Prototypes.

- Object keys can only be a String or a Symbol (more on Symbols later)
- Copying objects (cloning) is pretty weird

## On JS Inheritance with Objects and the Prototype Chain
[From Mozilla docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

JavaScript Inheritance doesn't work like C/C++/Java does because those are
(usually) OOP-based languages. JavaScript inheritance is based only on
Objects, and the Prototype Chain.

- Each Object has an internal "link" to its Prototype. Its Prototype also has
its own Prototype ("the prototype of the prototype"), and so on, continuing.

- When access to an Object's property is  requested, the accessor will look at
the Object's properties as well as traverse the internal link to the Object's
Prototype, to look for a match. If the match isn't found, the accessor will
then traverse the internal link of the Object's Prototype TO the Object's
Prototype's Prototype. This continues until a match is found OR end of chain.

- Sound performance intensive? That's because it is. Further, each time an
object's properties are iterated over, each property is itself enumerated. This
is a very expensive operation. The method `hasOwnProperty` is, according to
Mozilla, the only method to avoid traversal of the prototype chain.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain

## Shallow Clone and Deep Clone

#### Shallow/Bitwise Copy
Recall that bitwise copy in C/C++ uses functions like `memcpy`, which don't invoke the copy
constructor. Failing to invoke constructors can involve some bugs later on in the code.

*Bitwise copy is an implementation of shallow copy. Shallow copy just means copying data.*

#### Deep/Memberwise Copy
Recall that memberwise copy in C/C++ often involves the Rule of Three, including constructors and
the use of copy constructors in order to copy all of the data over. Slow but reliable.

*Memberwise copy is an implementation of deep copy. Shallow copy just means copying data and pointers.*

`Object.assign()` is used a lot to copy objects. Before we really can discuss copying or cloning of objects, we should first understand Deep Cloning and Shallow Cloning. It's notable that Object.assign does a shallow clone [see MDN's Deep Clone warning here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

![Deep Clone and Shallow Clone graphic](https://raw.githubusercontent.com/abstractmachines/abstractmachines.github.io/master/_posts/js-object-cloning.png)
*Image added in 2018 for clarity*

How do we practically deep cloning? WE can use custom code... we can use for in loops with that custom code...
we can use lodash's deepClone functionality, which is slow but commonly found in industry.

### Object Enumerable Properties

> Enumerable Properties: "On THIS object, not down the prototype chain"

An Object has Enumerable properties when its properties are its own properties,
instead of just being properties that belong to another object somewhere in the Prototype Chain.


**There are a lot of methods for Objects in JS on the MDN site. How can I intuitively tell the difference?**

To think simply about whether a method or context refers to the enumerability of an object, think about what it is.

If it's *enumerable*, it *has its own stuff.*

It makes sense now why `hasOwnProperty()` refers to whether an object's property is an enumerable one.

[See more on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties).

## Object iteration

> Note: Iteration over JS objects can be a bit weird. We generally use another data structure such as Maps, for iteration.

#### Object.entries(obj)
This method returns an array of object's Own Enumerable Properties, in key value pairs.

#### Object.keys(obj)
This does the same thing, but only returns/operates on keys, not values/pairs.

Recall the Object keys can only be a String or a Symbol.

#### Object.values(obj)
Same as above, only values instead of keys.

#### for in
[`for in`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in) iterates over the enumerable properties of an Object. It **returns unordered** results. (If you want order, use a for loop or similar).

`for in` is interesting because it enumerates over properties that are also in the Prototype Chain. MDN-recommended methods `.hasOwnProperty()` and `.propertyIsEnumerable()` can be used to. If you read the previous section, these should seem intuitive.

## JS Operators

Operators in JavaScript are pretty similar to C/C++. Remember that boolean
is a primitive type and that Boolean is a class wrapping that type with
functions you can inherit by using the new keyword and the Boolean data type.
View this is a analogy to inheritance - although it's important to remember
that JavaScript is prototype-based, not class-based (the `class keyword`
introduced in ES2015 is just syntactic sugar (see the Prototype Chain, below).

**`=>`**

 One interesting operator in JavaScript is the operator or `token` `=>` used
 in `ES6 Arrow Functions.` It serves as "fat syntax" for brevity.

 The triple equals operator `===` in JavaScript, as mentioned directly above,
 compares primitives by value, and objects by reference. We've seen this
 operator used for type checking, but in JavaScript there is some additional
 behavior, from object reference comparisons to implicit type conversions.

 > "In JavaScript objects are a reference type. Two distinct objects are never
 equal, even if they have the same properties. Only comparing the same object
 reference with itself yields true." - Mozilla
 https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects#Comparing_Objects
 https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators?v=test



## JS Functions

### JavaScript functions are first class objects

• **Can be passed an argument**
This means that a function can be passed as a function **argument,** and
then be executed later. It can even be returned, to be executed later.

This is one of the fundamental elements of functional programming:
>> **Functional programming:**

>>>**functions as arguments.**

Note how functions as arguments utilizes the **Inversion of Control** Pattern. More on IoC later.

• **Can be assigned to a variable**

• **Can be returned as a value**

Hence, all JavaScript functions can be `passed around` in this way.

JavaScript functions **return `undefined`** by default. So do variables that
are referenced without being defined first. (Unless you're using strict mode.)

For constructors called with the `new` keyword, the default return type is the
*VALUE* of the `this` parameter. If a function is not a constructor function,
default return value is undefined.

### JavaScript Functions and Return Values

JavaScript functions return `undefined` by default, unless the function has a
return value. This is very intuitive.

Use of constructors with the `new` keyword: the default return type is the
*VALUE* of the `this` parameter. If a function is not a constructor function,
default return value is undefined (or return value is specified return value).
This is also very intuitive.


### Primitives Passed By Value, Objects By Implicit Reference

Primitives (data types) are passed by value into functions, but any soft of Object is passed by "reference," implicitly. Passing an Object (a non primitive inherent to JS which inherits from Number, Function, Object etc, or a user-created Object) results in changes available outside the function's scope.


### Callbacks

> What are callbacks or "higher order functions"?

Callbacks are derived from functional programming. Callbacks are closures; a function within a function. Let's play with code:

```
function a(){

function b(){
console.log('b');
}

console.log('a');
b();
}

var c = a();

c;

console.log('c');
```

`a b c`

if we add `return b()` in a(), same results.

There's a lot more to say about callbacks, but we'll move on for now.

### JavaScript function scope

> Static scope: Lexical Scope or "Closure"

In order to discuss "closures", we first discuss "Lexical Scope."

**What's "lexical" mean?**

> "Lexical": relating to the words or vocabulary of a language."


>>>>So **lexical** just means "words, **wordy stuff** and stuff about words."


**The term "lexical analysis"**, in computer science, refers to the process of
converting characters, into **meaningful tokens** which can be used by a language
for specific purposes. **Tokenizers, lexers are tools** which perform these
actions. Together, lexers and parsers analyze a language's syntax. A parser just takes in input and outputs a data structure and structured, hierarchical representation of the output (thanks Wikipedia!) https://en.wikipedia.org/wiki/Parsing#Computer_languages

There are a few terms in computer science / software that refer to scoping using the term "lexical."

>>> **Static scoping == lexical scoping**

Lexical scoping just means static or block scoping. "Statically scoped" means that a block defines a new scope. C is lexically scoped, with variables being allowed static, automatic, or manual lifetime.

>>> **Dynamic scoping**

With dynamic scoping, we look locally for a variable's definition. If not found, we look **up the calling stack.**

**JavaScript has lexical scoping, and the global scope is the outer scope.** `let` and `const` ES6 features allow for block scoping.


> Most languages with C curly brace syntax are block scoped. JavaScript is not one of them.

JavaScript uses the same `{ block operators }` as C-syntax languages, but
JavaScript is weird, and so it uses function scope, not block scope. This is
unless we use ES6 or other techniques to block scope variables.

> Function scoping: This is how hoisting and closures are possible.

> **"The phrase scope bubbles up" means:** "When JavaScript tries to resolve an
identifier, it looks in the local function scope. If this identifier is not
found, it looks in the outer function that declared the local one, and so on
along the scope chain until it reaches the global scope where global variables
reside. If it is still not found, JavaScript will raise a ReferenceError exception."
(Wikipedia)


### Closures and scope

Accessing a variable outside of its immediate/local block scope is one way to describe a **closure**; a closure is usually expressed as an inner function and an outer function. Mozilla Development Network(MDN) gives a great definition:
“A closure is a special kind of object that combines two things: a function, and the environment in which that function was created. The environment consists of any local variables that were in-scope at the time that the closure was created.” https://developer.mozilla.org/en-US/docs/Web/JavaScript

**Inner functions have access to a wrapper function's variables.**
This is made possible by function scoped variables instead of block scoped variables.

See 2ality.com, Axel Rauschmeyer's blog, for more info on closures and hoisting.

Axel says "Closures don’t get snapshots of a certain point in time, they get “live” variables." This can lead to **inadvertent sharing of variables via closures.**

You can resolve this inadvertent sharing using an IIFE (Immediately Invoked Function Expression). **IIEFs restrict a variable's scope to current block.**

**IIEF's have some drawbacks, as they can make code a lot less testable.** In general, it's **often better to just name things** rather than have anonymous stuff executing in your runtime.

### When are closures interesting?

It really seems like the concept of a closure - meaning, a function within a
function in a function-scoped language instead of a block-scoped language, is
a pretty simplistic and boring idea, and that's because it is. The topic that
makes closures interesting as asychronicity; the inner function can outlive the
outer function.

### Hoisting, Event Bubbling, Delegation
This means that variable declarations "bubble in scope" to the containing element.
Function scoped variables allows those variables to be assigned anywhere in
the container, including in nested / "child" functions.

Hoisting doesn't make a variable "global", rather, it makes the variable
available "file-wide".

> "Thus a `var x = 1 statement` in the middle of the function is equivalent to
a `var x declaration statement at the top` of the function, `and an x = 1
assignment statement at that point in the middle of the function` –
**only the declaration is hoisted, not the assignment.**"
-Wikipedia on JavaScript

### Event Bubbling, Capturing, and Delegation
In JavaScript, scope bubbles up. This means that event listeners can be
written with more succinct handlers on the parent or ancestor elements in
the DOM, rather than on each child. Less code! Here's an example:

> Use Case: ul items all need click event handling. New items will be added by user.


Option A: Write handler for each li item:
```
ul
  li -> listener handled by click event listener/handler on li[0]
	li -> listener handled by ANOTHER click event listener/handler on li[1]
	li -> listener handled by YET ANOTHER click event listener/handler on li[2]
/ul
```
- lots of code, lots of browser memory
- dynamic user-added DOM elements not included / would require even more code

Option B: Write a single handler for well-chosen ancestor:
```
ul -> listener handles click events
  li -> click event bubbles up to ancestor, is handled there. No code here.
	li -> " "
	li -> " "
/ul
```
**Choose Option B.**
- Less code, less browser memory
- Handles any new li item user adds via JS event bubbling model
- Best practices: try to choose an Element which is unlikely to be removed
from the DOM; try to choose an element with spatial proximity for performance.
- **Event object, event.target, and Mozilla Event API's:**
Use event capturing/Mozilla API for Event.target.tagName name matching to
ensure desired child selector is the only node selected in DOM tree.
Another option is to use optional args for addEventListener().
Event Object is 1st argument of the event handler callback function:

```
const aParentDiv = document.getElementById('aContainer');

aContainer.addEventListener('click', (event) => {
	if(event.target.tagName = 'LI')
	{
		event.target.textContent = 'new text to add to this li.';
	}
});
```
 See [MDN API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
 for more information on Event Object and listeners, with
 `useCapture`:
 `EventTarget.addEventListener('event-type', callback, true)`

> Use Case: Removing/appending an li element child node without already having a reference to its parent. Use a button to remove the element.

- Traverse the DOM to find element's parent node
- Use .parentNode API
- Set up a "little DOM tree"

Sample code:
```
aParentDiv.addEventListener('click', (event) => {
  if (event.target.tagName == 'BUTTON') {
    let li = event.target.parentNode;
		let ul = li.parentNode;
		ul.removeChild(li);
  }
});
```

Corresponding HTML layout:
```
aParentDiv
	ul
		li
			button
		/li
	/ul
/aParentDiv
```

Note: **spatial locality** can become an issue with DOM bubbling, so you may
need to select a parent element closer to the li > button. If you have issues
with the other button elements on the page deleting things they aren't supposed
to after using this code, consider specifying the parent div rather than document:

`const listUl = aParentDiv.querySelector('ul')`;

... and then add the event listener to listUl instead of aParentDiv.

See also:
- [Node.insertBefore(newNode, referenceNode)](https://developer.mozilla.org/en-US/docs/Web/API/Node/insertBefore)

- [Node.previousElementSibling](https://developer.mozilla.org/en-US/docs/Web/API/NonDocumentTypeChildNode/previousElementSibling)


### Function Statements and Function Expressions

Overall the difference is often not well explained in most tutorials you find
online. One of the differences, according to Rauschmeyer, is that expressions
produce a value, and statements take an action. So `statements` are things like
if statements,  loops, and `"action oriented"` control structures, and
`expressions` are `value-producing` code such as **`a+b`.**

Whenever an identifier is NOT present, we know that we are dealing with a
function expression. However, if an identifier IS present, there are several
things that helps us identify between expressions and declarations.


```
function apple()
{
    var size = "medium";
    alert("Eat a " + size + " apple, fool");
    return "eaten";
}
var fruit = apple();
fruit();
```
Result: `Performs alert` then `returns undefined.`

### Function Expressions

In the above functions, you have the function invocation apple(). That function
invocation is an expression of a function invocation.

**These are expressions. Expressions don't "return." They "evaluate to."**

## C/C++ tends to use statements, not expressions
`var foo = 1;`

So this code:
```
function apple()
{
    var size = "medium";
    alert("Eat a " + size + " apple, fool");
    return "eaten";
}
```

This is just a function declaration/definition, nothing got called…

Calling `apple()` will invoke a function, via an expression, that evaluates
to `eaten.`

You could also go:
```
var newEval = apple();

newEval; // evaluates to `eaten`
```

but use `newEval;` NOT `newEval();` because newEval isn’t something that needs
to be invoked. It’s just something that holds the evaluated value computed from
running `apple()` expression.


> **Difference #1: Named or anonymous?**


"Function declarations" a.k.a. "function definitions" a.k.a. "function
statements" have an "identifier." This means that the function is named.

> Difference #1: "Function expressions" can be anonymous. Function declarations
cannot. So the moment we see an anonymous function, we know that that's a
function expression.

> One of the primary reasons for having named function expressions is for debugging.


> Function Declaration:

 ```
 function radFunk()
 {
   // do rad stuff
 }
 ```

> Function Expression:

 ```
 var x = function ()
 {
   // do rad stuff
 };
 ```

> Function Expression:

 ```
 var x = function yaGottaName()
 {
   // do rad stuff
 };
 ```


  Issue: The ins and outs of expressions
  http://www.2ality.com/2012/09/expressions-vs-statements.html

  Expressions create a value. Expressions can be function arguments, and can
	replace a statement. This is called an `expression statement.` The reverse
	doesn't hold; a statement can't be used in replacement of an expression.
	Think of an if statement, versus a ternary conditional expression.

> statement: if a, then b. Else, c:

  ```
  if (a)
  {
    a = b;
  }
  else
  {
    a = c;
  }
  ```

> expression: if a, then b. Else, c:

  ```
  var evaluation = a ? b : c;
  ```


> Another reason for giving a function expression its own name is for recursion,
so that that function may be called from within its own body.

> **Difference #2: function expressions are inside of () grouping operators:**

> Difference #2:  
>Function expressions are  inside of (grouping operators), such as `(function(){})`


> **Difference #3: Function declarations are often inside of function bodies.**

> **Difference #4: Context**

 > In JavaScript, context really matters. Check out the **apply, call, and bind**
 methods covered herein for more information.

> Final note: Function invocation via variable assignment is also called a Function Expression.


### Function constructors

```
new Function( arg1, arg2, functionBody )
```

```
var adder = new Function('a', 'b', 'return a+ b');
```



## Apply Call And Bind

This section is generally written from my own perspective, with some information gleaned directly from the [JavaScript is Sexy article on the "this" keyword in JS.](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/), as well as other aforementioned sources.

### The problem: the "this" keyword in JS

The *this* keyword is something C/C++ programmers are used to seeing (except in 'static' contexts).

> "This" in JavaScript depends completely in the context of the invocation. JavaScript is "function scoped", so "this" bubbles up.

Another way to say that is:

> In JS, this/context is defined by when/where the function is called, i.e. the
context of that invocation. In C++, Java, Ruby, and other languages, this/self
will always point to the object in which the method is defined. Not so with JS!

And THAT, creates a lot of problems.

Recall closures and "lexical scope" or "static scope".
In closures, the `this` reference "bubbles up."

Each scope just binds a different value of `this.` Since JavaScript is function
scoped, the `this` reference bubbles up to the global object. This means that
by default, `this` refers to the global object / `namespace` / window! That is
when the browser is concerned, of course. In Node, that global object may be
different.

In Chrome dev tools browser console:

```
> this

Window {speechSynthesis: SpeechSynthesis, caches: CacheStorage, localStorage:
	Storage, sessionStorage: Storage, webkitStorageInfo: DeprecatedStorageInfo…}
```

In JavaScript, just as in other languages, objects have a **this keyword**:

```
var animal = {
  species: "kitteh",
  age: 1,
  ageGetter: function() { return this.age }
}

animal.age; // 1

animal.ageGetter(); // 1
```


**So how to solve THIS problem?**

• **ES3:** Use `"that"` variable to copy this.property value over inside of function body. Then refern to "that.property" instead of "this.property".

• **'Modern JS':** Use `apply, call, and bind` to resolve the context of this, for borrowed functions, nested functions, and any context where a function is applied in a context in which it was not defined

• **ES6**: Use `arrow functions`

### Bind(): assigning a different "this" object when in an undesired context.
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind

> Return value: A new, bound function with the specified "this" value.

> Call and apply are some of the most common functions in JavaScript. Mozilla
says that "while bind() does a [[ construct ]], apply() and call() do a [[ call ]]."
That's why we are grouping call and apply together below, and bind separately
here; the nature of the return value.

The bind() method creates a new bound function.
When that function is invoked, that function has its `this` keyword set to the
provided value.
That "provided value" essentially means "a contextual this" (my definition).

**Traditional uses for bind include**:
• Set the "this" value on methods

• When calling a function from another function in a situation where you need to resolve context of this keyword

• Function currying by presetting parameters

• Borrowing methods

• Bind() is usually used when calling one function from another function, and
ensuring that the proper context (like scope) is used for the intent of the
function.

• While bind can technically be used to borrow methods, that is usually
left to call() and apply().

**Bind() Example 1: Vanilla JS**

```
this.x = 1 // 1

someObj = {
x: 2,
getX: function(){return this.x}
}

someObj.getX() // 2

```

What about if I create a variable within the global context, though?

```
const globalX = someObj.getX

globalX() // 1
```
Whoops, we are getting 1, instead of 2. That's because we are currently within
the `global context.`

So, **we need to bind it to the right context.**

**Solution: bind() this to proper context:**

```
const globalXNow = globalX.bind(someObj)

globalXNow() // 2
```

or, for brevity:
```
this.x = 1 // 1

someObj = {
x: 2,
getX: function(){return this.x}
}

const amod = someObj.getX.bind(someObj)

amod() // 3

```

Now we've bound that variable to the proper object context. You can clearly see
that in this way, functions can be shared as according to different contexts.
All you'd need to do to share a function between objects or contexts would be
to bind it to the context we want. Above, we could bind the context as either
global or 'someObj.' Pretty cool stuff - and it also illustrates why this
language is so confusing at times. Let's do another example.


**Bind() example 2: jQuery**

The JavaScriptIsSexy blog notes the following code.

We have a simple object with a clickHandler method that we want to use when a
button on the page is clicked​

(ps, note how `var` is used below. Well, don't use `var.` Use const or let!)
```
var user = {
 data:[
 {name:"T. Woods", age:37},
 {name:"P. Mickelson", age:43}
 ],
 clickHandler:function (event) {
 var randomNum = ((Math.random () * 2 | 0) + 1) - 1;
​ console.log (this.data[randomNum].name + " " +     this.data[randomNum].age);
 }
}
​```
```

The button is wrapped inside a jQuery $ wrapper, so it is now a jQuery object​,
And the output will be undefined because there is no data property on the button object​



```
$ ("button").click (user.clickHandler);

Cannot read property '0' of undefined
```


**Solution: bind() this to proper context**

> "Since we really want this.data to refer to the data property on the user
object, we can use the Bind (), Apply (), or Call () method to specifically
set the value of this." - JavaScript is Sexy

Change:

```
$ ("button").click (user.clickHandler);
```

To:

```
$ ("button").click (user.clickHandler.bind(user));
```

Understanding a bit more about JavaScript is always good. jQuery is easy to use
without understanding it completely; hopefully this clears up a lot of jQuery
you may see around!


### Call() and apply()

> Call and apply are some of the most common functions in JavaScript. Mozilla
says that "while bind() does a [[ construct ]], apply() and call() do a [[ call ]]."
That's why we are grouping call and apply together here; the nature of the
return value.


**Traditional uses of call and apply include:**
• To resolve context with this keyword

• To borrow methods (for example, to borrow Array methods such as slice or push
	to array-like objects that aren't actually arrays)

• With apply(), variadic functions


### Call()

> Call is very similar to apply(). Call() takes an argument list, like most functions do.

> Return value: The result of calling the function with specified "this" value and arguments.

### Apply()

> Apply(): assigning a different "this" object when calling an existing function.

> Apply() accepts a single array of arguments.

> Return value: Same as call(): The result of calling the function with specified "this" value and arguments.

> "Apply is very similar to call(), except for the type of arguments it supports.
You can use an arguments array instead of a named set of parameters. With apply,
you can use an array literal, for example, fun.apply(this, ['eat', 'bananas']),
or an Array object, for example, fun.apply(this, new Array('eat', 'bananas'))."
 Mozilla Docs
 https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply

It's important to note that in functional programming, functions aren't "called."
They're "applied." See the section on **partial applications** and **curried
functions.** For now, we will continue with apply().

Apply() is great for **variadic functions** (aka **variable arity functions**)
because apply() takes in arrays of arguments as parameters. So, great for
functions where it's less deterministic how many arguments may end up getting
passed in.

You can **apply** the contextual **this** to **other objects**:
```
var stripeyAnimal = {
  species: "zebra",
  age: 2
}

animal.ageGetter.apply(stripeyAnimal); // 2
```

### The arguments object

> The arguments object object is an array-like object available in every JS function.

Let's say you write something like
`var x = Array.prototype.slice.call(argument, 1);` - the return value you'll
get for `x` is `every element of the x object from index 1 onwards.`
```
function x (name) = {
  return Array.prototype.slice.call(arguments,2);
}
x(1,2,3,4,5); // 3 4 5
```

> Because the arguments object is an array-like object, we can use the Array's slice method as above by borrowing it using call().

**Here's another example with the arguments object using ES6's let.** Sort this array.

> Input: [111,2,66, 1]

```
function variadicStuff() {

var argsArray = Array.prototype.slice.call(arguments);
return argsArray.sort();

}

let sortMe = variadicStuff(111,2,66, 1);

sortMe; // invoke!

```
output:
> [1, 111, 2, 66]

**Let's use apply() and a variadic function** such as Math.max() which returns
the maximum number. The format will usually be

`function.apply(this, stuff);`

Let's use this:
```
var x = Math.max(1,2,3,4); // 4
```

for an ARRAY: we get NaN because Math.max cannot convert this array to a
number primitive.
```
var arr = [1, 3, 5, 7];

var maxish = Math.max(arr);

maxish; // NaN
```

So, use apply(): the first argument is null because there is no this in Math.max().
```
var arr = [1, 3, 5, 7];

var maxish = Math.max.apply(null, arr);

maxish; // 7 ... good!
```


## ES6

### Arrow Functions

• Arrow functions are basically function expressions written in a more compact way.
• Always anonymous, so of course arrow functions cannot be used for constructors
or anything requiring the `new` keyword.
• Arrow functions allow for a simpler handling of this, because arrow functions
do not bind their own this, OR their own arguments. For this reason, arrow
functions are often used in nested JavaScript functions:

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions

```
function Person() {
  // The Person() constructor defines `this` as an instance of itself.
  this.age = 0;

  setInterval(function growUp() {
    // In non-strict mode, the growUp() function defines `this`
    // as the global object, which is different from the `this`
    // defined by the Person() constructor.
    this.age++;
  }, 1000);
}

var p = new Person();
```

In ECMAScript 3/5, the this issue was fixable by assigning the value in this to
a variable that could be closed over.
```
function Person () {
  .... blah blah
  var that = this;

  setInterval()
  {
    that.age = stuff
  }
}
```

We could also bind it, and return a bound function such as
`that.age.bind(this)` or similar,

Or just use an arrow function:
```
function Person(){
  this.age = 0;

  setInterval(() => {
    this.age++; // |this| properly refers to the person object
  }, 1000);
}

var p = new Person();
```

Source:
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions


### Const let var

Declaring `var` can be problematic because it can bubble up in scope (see the scoping sections of this tutorial). Some engineers may say "var is global." While that's a simplistic way of looking at it, let's move forward with that assumption and discuss const and let for variabe declaration.

> There are several languages in which `let` is similar to `const`.
We can also consider the JS `Object.freeze` for immutability.

### Const, requiring initialization upon declaration, sets a "read only"
### reference to a value.

**Const is not considered immutable; for immutability, use Object.freeze().**

What const means is that the **variable identifier cannot be reassigned.**

> Use const to do things like push() an item into an array, but you don't want
the overall properties / name of that array to change.

### Let limits scope of variables to the block/statement/expression where declared.

> use let to "block scope" variables. This is unlike the `var` keyword which
defines a variable globally.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let

### Var keyword defines a variable globally.

**Think about block scoped versus function scoped variables when using the
var keyword.** Doesn't the var keyword include the native "vanilla JS behavior"
we know so well with hoisting and bubbling up scope? Yes, it does.

> use "var" for when you want the entire enclosing scope to have access to
the SAME variable. Better yet, don't use var. This is ES6. Use const and let.

### Const and Let: the temporal dead zone

In older JavaScript, we could use a variable inside a block before declaring it.
When we're using const and let, however, we need to pay closer and more careful
attention to our usage because we can't reference a variable prior to declaration
in the block-scoping type of scenarios created by const and let. Not even
hoisting will always save you, so when using const and let, always declare
everything in the block prior to use.


## ES6 Iterators Implicit and Explicit

### for in : pre ES5
We'll recall using `for item in items` syntax with JavaScript.
Well, that syntax is pre-ES5 and is used for objects (an unordered collection
of elements). Contrary to how we think about arrays, the elements that `for
in` constructs loop over are not order-guaranteed because objects in JS are not
ordered items.


ES5 introduced the .forEach() property:

```
var arr = [ "doge", "so", "wow"];

arr.forEach(function(value,index){
  console.log("index [" + index + "]"" + value);
  });
```
output:
>index [0]doge index [1]so index [2]wow


### Implicit ES6 Iterators : for of

For of syntax is very similar to for in syntax, just a bit simpler.
```
var obj;
var list = [0, 1, 2, 3]
for (let obj of list) {
    console.log(obj)
}
```
output:
> 0 1 2 3

### ES6 Iterators: Explicit ( next(), .done, and .values/keys/entries )

A Javascript iterator provides a next() method. Very similar to iterators and
containers in C/C++, or Java collections.

A generator function that returns a new object each time you call `next`. There
is a done property, and there are other properties like ... .values, .keys,
.entries.

More on this later.
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators


And while we're at it, we'll mention ES6 `Symbol.` More on this, later, too.


### Rest parameters for variadic functions (ES6)

More soon on this.



### ES6 and Angular
#### Refactoring for future proof Angular 1.5 -> 2 + ES6 JS: the future
• **Creating future-proof 1.5 to Angular 2 with incremental steps:**

>`Use component directives instead of controllers`: like `Web Components`...
Angular 1.5 introduced the .component. For future-proof Angular 1.5,
use `controllerAs` syntax in component directives. You can do just about
everything you could do in `.directive` with the `.component` keyword instead,
and more. In Angular 2, controllers are going away, and we replace them
with the Web Components-like component directives. Remove controllers from
routers (ngRoute or UI-Router). Note that ngRoute is also fairly deprecated.


>`Use arrow functions wherever possible`:
A simpler way to bind to the parent's this in nested functions. Not appropriate
for Angular controllers, because Angular controllers "control" the context of
"this". `Not appropriate for` constructor function or any function using the `new` keyword, as that would require a `name` and arrow functions are anonymous.


>`Angular: Put logic in services (classes), and UI code in component directives`:
This is what modern Angular consists of: logic in services, and UI code
in directives/components - that is it! Classes map perfectly to services!


> `Use const over let, so you can mutate later.`

## Common JavaScript Design Patterns

### Module Pattern : "Encapsulation"
The module pattern just refers to JavaScript modules, which are analogous to
classes; the module pattern addresses access (public, private, protected).

Protected data is usually wrapped in an IIEF to protect access for private data.

### Singleton Pattern : Globals / State
We're familiar with the Singleton Design Pattern for sharing of state and
similar globally required data with reference counting or similar constructs.

### The Observer and Publish Subscribe Patterns: Streams, Observables,
### Broadcast, Events, Data Binding
We're familiar with the Observer Pattern, the more restricted/"exclusive"
version of the Publish-Subscribe Design Pattern.

#### Use When:
- broadcast situations such as an event bus
- Reactive Programming / Streams / events / clicks
- systems where control flow is difficult to follow in real time and hence
the engineer's focus is placed on communication patterns instead of control
flow (Gang of Four)
- contexts in which a part of the system will be updated, and that
part of the system needs to update another part of the system without needing
to know its implementation details (Gang of Four).

#### Consequences (Gang of Four):
- update another part of the system without needing to know its implementation
details (Gang of Four). Yay, good!
- update another part of the system without needing to know its implementation
details (Gang of Four). Noo, bad! You don't know WHAT is being updated, and
this may cause issues with **unexpected updates** and **an update not
knowing the consequences, system-wide, of that update.** Basically the
consequences of Observer/Publish Subscribe are **hey, what happened?**

#### What's the difference between Observer and Publish Subscribe Patterns?
**The difference is whether you have to register - hence, abstraction.**

Recall that PubSub Publisher broadcasts all messages non exclusively to any
Subscriber who will listen or subscribe. The Observer Design Pattern is slightly
different because the Observed or Subject or Observable maintains a list of the
Subscribers in a data structure, and the Observers must request access).
Methods for Attach() and Detach() and Notify() (and subscribe() and
unsubscribe() ) are relatively intuitive.

Observer hence **notifies its registered subscribers**, and Publish Subscribe
**broadcasts messages to its listeners which it knows nothing about.**

### What's an example of an Observer? Angular Watchers.

### What's another example? Streams are 'Observables' or 'Subjects'

### Observables, Promises, Reactive Programming, Streams:
While Promises are often used in asynchronous, Reactive Programming tends to
use more Streams, which are Observables. Observables are Promises with
additional features such as being cancellable (via unsubscribe() or detach() )
whereas Promises do not offer the ability to cancel or unsubscribe.

## Inversion of Control Pattern (Dependency Injection)
Inversion of Control is the inversion of the expected relationship between
object and dependency; it is expected that an object will retrieve its own
dependencies. When passing a function in as an argument, the dependency is
passed directly to the object, and can hence influence the object. This is
referred to as "Inversion of Control".

For an interesting and oppositional opinion on dependency injections,
read DHH's
[Dependency injection is not a virtue](http://david.heinemeierhansson.com/2012/dependency-injection-is-not-a-virtue.html).
David is essentially saying that DI is used to make inflexible languages,
flexible.


... To be continued in Part 2!

<span>Category: {{ page.categories }}</span>