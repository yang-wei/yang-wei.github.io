+++
title = "Javascript Destructuring Assignment and Spread Operator"
date = "2016-01-15"
slug = "2016/01/15/javascript-destructuring-assignment-and-spread-operator"
categories = ["es6", "destructuring", "javascript", "spread"]
description = "In this post we dive deep into one of the most excited ES6 feature - destructuring (we also include some discussion related to spread operator)"
+++

We will first discussed how destructuring and spread operator can be used in ES6 - in arrays and objects. Then we will look at a few examples and also discuss some interesting quiz.

<!-- more -->

### arrays

```js
var array = [1, 2, 3, 4];
var nestedArray = [1, 2, 3, 4, [7, 8, 9]];

var [a, b, c, d] = array;
console.log(a, b, c, d)
// -------- 1  2  3  4

var [a, , , d, [x, y, z]] = nestedArray;
console.log(a, d, x, y, z)
// -------- 1  4  7  8  9
```

We don't have to match the full array
```js
var [a, b, c] = array;
console.log(a, b, c)
// -------- 1  2  3

// spread operator
var [a, b, ...c] = array;
console.log(c);
// [3, 4]
```

**Remember: when using spread operator, you must apply it as the last argument.**
```js
var [...head, d] = array;
// Uncaught SyntaxError: Unexpected token...
```

When destructuring variable exceeds its matched array, undefined is returned
```js
var [a, b, c, d, e] = array;
console.log(e);
// undefined
```

But we can always back it up by set its default value
```js
var [a, b, c, d, e = 5] = array;
console.log(e);
// -------- 5
```

We can clone array easily
```js
var [...clonedArray] = array;
console.log(clonedArray);
// [1, 2, 3, 4]

// clone the nested one
var [,,,, [...clonedNestedArray]] = nestedArray;
console.log(clonedNestedArray);
// [7, 8, 9]
```

Swap variable like a boss
```js
var a = 1, b = 2;
[b, a] = [a, b];
```

If you had writing some JavaScript before, here is how you used to access arguments passed into a function.
```js
function foo() {
  return Array.prototype.slice.call(arguments, 0);
}

foo(1,2,3,4,5) // [1, 2, 3, 4, 5]
```
We can now refactor it into a shorter code:
```js
function foo(...args) {
  return args;
}

foo(1,2,3,4,5) // [1, 2, 3, 4, 5]
```

### Object
```js
var object = {a: "A", b: "B", c: "C"};
var nestedObject = {a: "A", b: "B", c: "C", x: {y: "Y", z: "Z"}};

var {a: A, b: B, c: C} = object;
console.log(A, B, C);
// ------ "A" "B" "C"

var {a: A, b: B, c: C, x: X} = nestedObject;
console.log(X);
// {y: "Y", z: "Z"}
```

When we only need a value
```js
var {b: B} = object;
console.log(B);
// ------- "B"
```

Similar to array, we can set a default value
```js
var {b: B, d: D = "D"} = object;
console.log(B, D);
// ------- "B" "D"

// and it returns undefined when keys are not match
var {a: A, b: B, d: D} = object;
console.log(A, B, D);
// -------"A" "B" undefined
```

For every object destructuring we have to write `{ keys: newVariable } explicitly and this is very verbose. Let's enter short hand method:
```js
var {a, b, c} = object;
console.log(a, b, c);
// -------"A" "B" "C"
```

It might seems confuse at first but this is the short hand of
```
var {a: a, b: b, c: c} = object;
// new variables a, b, c are created
```
Do not use short hand method if you want to create different variable name.

Continue to other examples:
```js
var object = {a: "A", b: "B", c: "C"};
var nestedObject = {a: "A", b: "B", c: "C", x: {y: "Y", z: "Z"}};


var {a, b, c, x} = nestedObject;
console.log(x);
// { y: "Y", z: "Z" }


var {b} = object;
console.log(b);
// ------- "B"

var {b, d = "D"} = object;
console.log(d, d);
// ------- "B" "D"

// and it returns undefined when keys are not match
var {a, b, d} = object;
console.log(a, b, d);
// ------- "A" "B" undefined
```

Using spread in object destructuring failed =(
```js
// error
// var {a: A, b: B, c: ...C} = object;
// console.log(A, B, C);   <-- error
// use the shorthand method

var {a, b, ...c} = object;  // es7
console.log(c); // {c: "C"}
```   

We used to write long code like this:
```js
var John = {name: "John", age: 20};
var Marry = {name: "Marry"};

function getAge(person) {
  age = person.age || 18 // default value
  return age
}
getAge(John);  // 20
getAge(Marry); // 18
// with es6 
function getAge({age = 18}) {
  return age
}
```

### Example

Let's see some pratical usage.
Eg: optional arguments for functions
```js
function sum(...numbers) {
  return numbers.reduce((n, total) => {
    return n + total
  }, 0);
}
sum();   // 0
sum(1, 2, 3);   // 6
sum(1, 2, 3, 4, 5);   // 15

// more abstraction <-- out of topic
function math(equation, ...numbers) {
  return numbers.reduce((n, total) => {
    return equation.call(null, n, total);
  });
}

const add = (a, b) => { return a + b; }
let sum1 = math(add, 1, 2, 3, 4);
// 10

const times = (a, b) => { return a * b; }
let product1 = math(times, 1, 2, 3)
// 6
```

#### Example on OSS
Let's look at some example on some great open source project - [redux](https://github.com/rackt/redux).
In redux source code there is a utils function - [`compose`](https://github.com/rackt/redux/blob/d2969b5e5a1fcc1489feec0a4fcc06f92e1a3e6b/src%2Fcompose.js) which is used to turn our verbose code into a nicer one.
```js
function (arg) { return fn1(fn2(fn3(arg))); }
```
into
```js
function(arg) { compose(fn1, fn2, fn3)(arg) }
```
Here is the shorten source code and explaination
```js
export default function compose(...funcs) {   // here, a list of function(s) is expected: fn1, [fn2, fn3...]
  return (...args) => { // it return a composed function which expects a list of argument(s): arg1, [arg2, arg3... ]
    // args is now an array: args = [arg1, arg2, ...]
    const last = funcs[funcs.length - 1]
    const rest = funcs.slice(0, -1)
    
    return rest.reduceRight((composed, f) => f(composed), last(...args)) 
    // the first part in reduce right is not so interesting, we just keep chaining functions
    // in the second part - last(...args)
    // since our last function will accept argument like last(arg1, arg2...) instead of last([arg1, arg2...])
    // we turn [arg1, arg2, ...] into arg1, arg2, ...
    // by using rest
  }
}
```

To simplify how the `args` being changed:
```js
function inspect(...args) {
  // when this function is called
  // args = ["a", "b", "c"]
  
  console.log(args)  // ["a", "b", "c"]
  console.log(...args) // "a" "b" "c"
}
inspect("a", "b", "c")
```

### Quiz
@kangax published an [ES6 JavaScript quiz](http://perfectionkills.com/javascript-quiz-es6/) and there are fews(Q3, Q7, Q11) related to destructuring and spread operator. Let's have fun with them.


#### 1 (quiz Q3 from ES6 JavaScript quiz)
```js
let x, { x: y = 1 } = { x }; y;
```
Let break it down piece by piece:
```js
let x, // x = undefined
   { x: y = 1 } = { x }
   // here a new variable y is created with default value 1
   // but since x is undefined on right hand side
   // we can think of it as
   // { x: y = 1 } = { }
   y; // when we call this y returns 1
```

#### 2 (quiz Q7 from ES6 JavaScript quiz)
```js
[...[...'...']].length
```
First I was scared by the syntax. But the `'...'` is actually a string. To make it clear:
```js
[...[...'str']]
```
Since string is iterable so `...'str'` will give us `["s", "t", "r"]`.
Then the next spread `[...["s", "t", "r"]]` will give us `["s", "t", "r"]`.
So the answer is clear.

#### 3 (quiz Q11 from ES6 JavaScript quiz)
```js
((...x, xs)=>x)(1,2,3)
```
As stated above in the array section, spread operator only can be applied as the last argument. So this will throw us error.

#### 4
```js
let { a: { length: a } } = { a: "AAA", b: "BBB" }
console.log(a)
```
You guess it !

### Conclusion
Destructuring is one of the most excited feature in ES6. As you see in quiz, destructuring can turned into difficult-to-understand code if you are not careful. So happy destructuring !!!
That's all for now, I hope you enjoy the discussion.

 * [Gist - Several demos and usages for ES6 destructuring](https://gist.github.com/mikaelbr/9900818)
 * [ES6 JavaScript Destructuring in Depth](https://ponyfoo.com/articles/es6-destructuring-in-depth)
 * [redux](https://github.com/rackt/redux)
 * [ES6 quiz](http://perfectionkills.com/javascript-quiz-es6/)
