+++
title = "Clojure Strings Are Sequence"
date = "2015-09-23"
slug = "2015/09/23/clojure-strings-are-sequence"
categories = ["string"," sequence"]
description = "Map over string in Clojure gives us a sequence, let's see how to solve this."
+++

As a newbie to Clojure, I am sure all of us will meet one problem when dealing with string. Let's say we have a long string.
```clojure
"This is a long string !"
-> "This is a long string !"    // clojure will give us back this
```
<!-- more -->
What if we want to remove all spaces ? Let's say we'll solve this by filter through the text and apply a function to check if it's a space. That's simple. 

In JavaScript, we'll probably write something like this:
```javascript
var str = "This is a long string !";
str.split("").filter(function(c) { return c !== " "}).join("")
// "Thisisalongstring!"
```

Since `Array.prototype.filter` is a method for array, so firstly we turn string into array by using `split` method. After filtering through it we will join it back.

What if we try to do this in Clojure:
```clojure
(str (filter #(not= % \space) "This is a long string"))
-> "clojure.lang.LazySeq@f3440d65"
```

Let's see why this doesn't work in Clojure and find a solution for it.
In Clojure, strings are sequences of characters. And Clojure characters are Java characters:
```clojure
\e
-> \e
\newline
-> newline
```

Let's concat some characters using the `str` function:
```clojure
(str \c \space \l \space \j)
-> "c l j"
```

As I said, strings are sequences of characters. So when we call sequences function in Clojure on strings, we will get a sequence(consistency!). Let's first filter through the string and remove the space.
```
(filter #(not= % \space) "This is a long string")
-> (\T \h \i \s \i \s \a \l \o \n \g \s \t \r \i \n \g)
```

We can see at first strings are turned into sequences and get mapped to apply our function `#(not= % \space)`. Indeed we can see what does it looks like:
```
(seq "This is a long string")
-> (\T \h \i \s \space \i \s \space \a \space \l \o \n \g \space \s \t \r \i \n \g)
```

Since we know how Clojure `str` works, it's very tempting for us to do:
```
(str (filter #(not= % \space) "This is a long string"))
-> "clojure.lang.LazySeq@f3440d65"
```
The problem is this won't work since we are passing a sequence of characters to the function `str` instead of strings or characters.

### Solution 1
The possible solution to this is using the `apply` function. Let's see how `apply` works:
```
(apply f seq)
```
`apply` unroll the parameter `seq` and apply function `f` to them (**Note: `apply` can accept [one more optional parameter](https://clojuredocs.org/clojure.core/apply).**). Seems like we can solve our problem now:
```
(apply str (filter #(not= % \space) "This is a long string"))
-> "Thisisalongstring"
```

### Solution 2
Another solution is to use `clojure.string/join` which is a [wrapper for `(apply str args)`](https://github.com/clojure/clojure/blob/a752736c1a14dce31e2f1cc30adde741328b4b12/src/clj/clojure/string.clj#L183).
```
(clojure.string/join "" (filter #(not= % \space) "This is a long string"))
-> "Thisisalongstring"
```
Boom !

### Conclusion
Clojure strings are sequences of characters and Clojure characters are Java characters. When a sequence function is use on string, we have to be careful on the result which is probably a list. To deal with that, we can always use the great `apply` or it's wrapper for `(apply str ...)` - `clojure.string/join`.
