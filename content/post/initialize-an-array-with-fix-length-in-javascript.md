+++
title = "Initialize an Array With Fix Length in Javascript"
date = "2015-09-14"
slug = "2015/09/14/initialize-an-array-with-fix-length-in-javascript"
categories = ["array"," javascript"]
description = ""
+++

In JavaScript, we love array. Furthermore, since the wonderful **Array Extras** added in ES5, it gives us the power to program in a more expressive way with array.

```js
var friends = ["Ali", "Winson", "John"]; 
var sayHiToFriends = friends.map(function(friend) {
  return "Hi " + friend;
});
console.log(friends); // ["Ali", "Winson", "John"]   
console.log(sayHiToFriends); // ["Hi Ali", "Hi Winson", "Hi John"] 
```
<!-- more -->
Note that friends value doesn't change. `Array.prototype.map` return a new array for us. Immutability huh? 

In some case, we might need to initialize array with a fix length so that we can iterate over it with method like `Array.prototype.map` etc. So how do we initialize an array? 

In JavaScript, we can create array with 2 ways:

#### 1. The new syntax
```js
var arr = new Array(5);
arr.length;   // 5
```

#### 2. Array literal
```js
var arr = [1, 2, 3, 4, 5];
arr.length;   // 5
```

Obviously, if we want to init an array with a fix length, the new syntax seems easier. Imagine how do we create array with length 100 with the array literal?
Sure we can do something like:
```js
function createArrayWithLength(n) {
  var arr = [], i;
  for(i = 0; i < n; i++) {
    arr.push(i); 
  }
  return arr;
}
createArrayWithLength(100).length; // 100
```
But it's not expressive. Compare this to:
```js
var array = new Array(100);
array.length; // 100
```
Boom, short and clean. However, the array created is filled with *undefined*. Thus make it non-iterable.
```js
new Array(100).map(function(val, i) {
  return i;
}); // [undefined x 100]
```

### So what is the solution

Here are some solutions implemented by myself and also collected from resources online.

#### 1. Function.prototype.apply 
```js
Array.apply(null, Array(100))
.map(function(val, i) { return i }); }) 
// will give us [0...99]
```

#### 2. Array.prototype.fill 
With es6 now we have many additional array method. One of them can help us to achieve what we want. 
```
Array(100).fill(null)
.map(function(val, i) { return i }); })
// will give us [0...99]
```
However, this method is an es6 method so [not every browser support](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/fill#Browser_compatibility) it. You might want to consider using [babel](babeljs.io) compiler or [polyfill](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/fill#Polyfill) for now.

#### 3. Array.from
Another cool feature from es6 will also complete our task.
```
Array.from(new Array(100), function(val, i) { return i }); })
```
This is also an es6 feature, so if compatibility is what you considering, please use polyfill.

#### 4. Utility library
Using library like [lodash](https://lodash.com/docs#range) can make our code looks cleaner.
```
_.range(100)
```
> Protip: If you use node, you can require smallest [lodash.range package](https://www.npmjs.com/package/lodash.range) from npm.

### Recap
We had seen 4 solutions to do this in JavaScirpt. By using `Function.proptotype.apply` we can initialize an array with a fix length or incremental value. Moreover, we can achieve the same thing more elegant by using es6 feature. Finally, never forget library like lodash or underscore too =)

### Reference
 * [ECMAScript 6’s new array methods](http://www.2ality.com/2014/05/es6-array-methods.html)
 * [The array literal notation [] is preferrable](http://jslinterrors.com/the-array-literal-notation-is-preferrable/)
 * [MDN Array.prototype.fill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/fill#Polyfill)
