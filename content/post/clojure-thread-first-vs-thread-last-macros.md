+++
title = "Clojure Thread First vs Thread Last Macros"
date = "2015-11-18"
slug = "2015/11/18/clojure-thread-first-vs-thread-last-macros"
Categories = ["clojure"," thread-first", "thread-last"]
description = "Clojure thread first -> and thread last ->> explaination in detail."
+++

In this post, I am going to show you {when|how} to use Clojure marcos, [`->>` aka thread-last](https://clojuredocs.org/clojure.core/-%3E%3E) and [`->` aka thread-first](https://clojuredocs.org/clojure.core/-%3E). In some case, `->` and `->>` may perform the same operation if you do not pay enough attention. So I will also show you what's the difference between them. Note that `doc ->` and `docs ->>` din't make sense for me at first, so if same thing happened to you I hope that this post will make it clear.

<!-- more -->

If I am coding a function in Clojure, I would not think to write in macro firstly(maybe I am still new to it?). Macros like `->` and `->>` only come in my mind when it comes to refactoring. They are not neccessary in our program but they will make it elegant.

To explain how both of these macros work, let's us solve a quiz together.
> [Write a function which calculates factorials.](http://www.4clojure.com/problem/42#prob-title)

I will write a function which accept an integer as parameter, and output the result.
```clojure
(factorial 3) ;; 6
```

To help you revise what's factorial:
```
1 factorial is expressed as 1! = 1 = 1
2 factorials is expressed as 2! = 2 x 1 = 2
3 factorials is expressed as 3! = 3 x 2 x 1 = 6
...
```
Easy right?
First of all we need to make up the vector which is a sequence of decrement of our initial value until 1. If we have number 5, we want to have a vector of [5, 4, 3, 2, 1] first. 

My first attempt will be:
```clojure
(defn factorial-seq [n]
 (next (range (inc n))))
 
(factorial-seq 5) ;; (1 2 3 4 5)
```
You might have another solution in mind but I purposely make it as nested as possible for this post. Now we have a few nested expressions in our code and this does not make it very readable. We can try `comp` to reduce the number of parentheses. Here is my 2nd attemp:
```
(defn factorial-seq [n]
 ((comp next range inc) n ))
 
(factorial-seq 5) ;; (1 2 3 4 5)
```
This is better but when we read our code, we have to read it from inside to outside. The flow is opposite. We can do better. What if I tell you we can make write our code into the correct flow? Enter enter thread-last `->>` macros.

My third attempt using ->> will be
```clojure
(defn factorial-seq [n]
 ( ->> (inc n)
       (range)
       (next)))
       
(factorial-seq 5) // (1 2 3 4 5)
```
This is much readably. So we take our argument:

 1. Increase it by `inc`
 2. Then we compose a list by using `range`
 3. And finally we remove the first element in our list by using `next`

Wow !

What about the thread-first `->` macro, will it work in this case ?
```clojure
(defn factorial-seq [n]
 ( -> (inc n)
       (range)
       (next)))
       
(factorial-seq 5) // (1 2 3 4 5)
```
Wow seems like it's working. So let's move on =)

Continue to our quiz, now we have a list where we just have to multiply each of them into a number. I am going to use `reduce` to do this(although other like `apply` will work as well). Because we are using the thread macro, we can read our code in the correct order of what we are thinking. In this case we just need to add the `reduce` function into our last form of `thread`.

```clojure
(defn factorial [n]
 ( ->> (inc n)
       (range)
       (next)
       (reduce *))) ;; reduce them by multiplying
       
(factorial 5) ;; 120
```
Don't you think this is cool? Let's see another brother `->`.
```clojure
(defn factorial [n]
 ( -> (inc n)
       (range)
       (next)
       (reduce *))) ;; reduce them by multiplying
       
(factorial 5) ;; IllegalArgumentException Don't know how to create ISeq
```
Oops, we are in trouble. And we know that it is caused by the `reduce` function. Let's use `macroexpand` to see what's happening under the hood.
```clojure
(macroexpand-1 '(->> (inc 5) (range) (next) (reduce *)))
;; (reduce * (next (range (inc 5))))
;; (reduce * ...) <-- let's simplify it

(macroexpand-1 '(-> (inc 5) (range) (next) (reduce *)))
;; (reduce (next (range (inc 5))) *)
;; (reduce ... *) <-- let's simplify it
```
Ahhh, now it's clear. When `->> aka thread-last` is used, the value from the previous form will be passed as **last item**(... in our example above). Meanwhile, when `-> aka thread-first` is used, the value from the previous form will be passed as **first item**. Are their names (thread-first, thread-last) make sense now? Also if you look at the function before `reduce`, ie: `range`, `next`, you know that both these functions work with one argument. That's why using `->>` and `->` dit not make differences.

Note that in Clojure, functions that deal with vector or list will take them as the last argument, ie: `reduce`, `map`. On the other hands, functions that deal with maps or stringn will take them as the first argument, ie: `get-in`, `split(string)`, `replace(string)`.
That means in most cases, we use `->>` when dealing with vector, list while `->` when dealing with map, string.

## Bonus: thrush
When I try to understand these 2 macros, I came across a new word - **thrush**. **thrush** sounds like `->>` and `->` at first, but as Fogus explained in [his post](* http://blog.fogus.me/2010/09/28/thrush-in-clojure-redux/), thread-last and thread-first macros will treat your forms as list, so code like:
```clojure
(defn factorial-output [n]
 ( ->> (inc n)
       (range)
       (next)
       (reduce *)
       #(str "Answer is " % "!")))
;; (factorial-output 120)
;; <user$factorial_output$fn__861 user$factorial_output$fn__861@4ca567c6>
```
will give us something not expected. Because the function `#(str "Answer is " % "!")` is not treated as function by macro `->>`.
```clojure
(macroexpand-1 '(->> (inc 5) (range) (next) (reduce *) #(str "Answer is " % "!")))
;; (fn* [p1__866#] (str "Answer is " p1__866# "!") (reduce * (next (range (inc 5)))))
```
Just like pointed in the blog post, we can create a `thrush` function like:
```clojure
(defn thrush [& args] 
  (reduce #(%2 %1) args))
```
Then use it in our function.
```clojure
(defn factorial-output [n]
 (thrush (inc n)
       range
       next
       #(reduce * %)
       #(str "Answer is " % "!")))

(factorial-output 5)
;; "Answer is 120!"
```
Hooray. Now we don't even need to have parentheses for our forms, ie: `range`, `next`.

## Read more
 * http://debasishg.blogspot.jp/2010/04/thrush-in-clojure.html
 * http://blog.fogus.me/2010/09/28/thrush-in-clojure-redux/
