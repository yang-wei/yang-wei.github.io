+++
title = "A Better Typeof in Javascript"
date = "2014-11-16"
slug = "2014/11/16/a-better-typeof-in-javascript"
categories = ["typeof","javascript","array"]
description = "Check type of array, function in javascript."
+++

In Javascript, there is a `typeof` operator to indicate the type of operand provided.
However, `typeof` only return object or primitive value.

{% blockquote MDN https://developer.mozilla.org/en-US/docs/Glossary/Primitive Primitive %}
A primitive (primitive value, primitive data type) is data that is not an Object and does not have any methods.
{% endblockquote %}

<!-- more -->
So in short,

 + primitive - `String`, `Number`, `Boolean`, `Null`, `undefined`, `Symbol`(new in ECMAScript 6)
 + non-primitive - object including `Array`, `Function`, and `Object` 

This mean that if we use `typeof` to test primitive values, we will get the result we want except some strange cases.
```javascript
typeof true === 'boolean'
typeof 100 === 'number'
// strange
typeof NaN === 'number'
typeof null === 'object'
```
This [post](http://www.2ality.com/2013/10/typeof-null.html) explains the strange case. Now let's see what happened when we test non-primitives.
```javascript
typeof function() {} === 'object'   // we want function
typeof ["Obviously", "is", "an", "array"] === 'object' // we want array
typeof { name: 'obj' } === 'object' // ok this is good
typeof new Date() === 'object'  // we want date 
```
So to get the type of those non-primitives, we will use `Object.prototype.toString`.
```javascript
var toString = Object.prototype.toString;

toString.call(function() {}) === "[object Function]"
toString.call(["Obviously", "is", "an", "array"]) === "[object Array]"
toString.call({ nane: 'obj' }) === "[object Object]"
toString.call(new Date()) === "[object Object]"
```
Here, we use call to borrow `toString` function from Object.prototype. So to make implement this in real work program, we can create a new function.
```javascript
// create a getRealType function to detect array, function correctly
var realTypes = {
  "[object Function]": "function",
  "[object Array]": "array",
};

function getRealType(obj) {
  var t = Object.prototype.toString.call(obj);
  if(realTypes.hasOwnProperty(t)) {
    return realTypes[t];  
  }
  return typeof obj
}
getRealType([1,2,3]) === 'array' // yeah !
```
Then our new function will be able to return the correct type of `function` and `array`. Of course we can extend our function to detect `Date`, `RegExp`. Or we can get fancy by using `Sting..prototype.slice` to make thing easier.

```javascript
var toString = Object.prototype.toString,
    type;

function getRealType(obj) {
  type = typeof obj;
  if(type === 'object') {
    // We know we will get something like '[Object Array]', what we want is 'Array'
    type = toString.call(obj).slice(8, -1);
  }
  return type;
}

getRealType([1,2,3]) === 'Array'
getRealType(new Date()) === 'Date'
```
###Conclusion
Although sometime Javascript `typeof` doesn't provide the detail information we want, we can make it better by using `Objecet.prototype.toString`. So when we want to detect `number`, `string` etc, `typeof` is really easy and handy. When it comes to `array`, `function` etc, `typeof` returns `object`, we can use the method above.

###Resource
 + https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof
 + https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString
 + https://developer.mozilla.org/en-US/docs/Glossary/Primitive
 + http://www.2ality.com/2013/10/typeof-null.html
