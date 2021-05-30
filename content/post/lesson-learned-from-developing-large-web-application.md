+++
title = "Lesson Learned From Developing Large Web Application"
date = "2015-07-24"
slug = "2015/07/24/lesson-learned-from-developing-large-web-application"
categories = [""]
description = "Thing learned when developing Tech-Train"
+++
Recently, I've been building **Tech Train** - which is a platform for students to enchance their web development skill. Until now this service is only release for particular group of students, so I'm not going to talk much about it. I can say it's a small clone(means not as much feature as) of [codecademy](https://www.codecademy.com) and [stack overflow](http://stackoverflow.com/).

<!-- more -->

Small team of us begin to build(part-time-ly) this around Jun 2015 and it took almost 2 months to complete. After 1839 commits, 469 issues and 399 closed pull requests on GitHub, it's finally ready for production. Technology is not in the scope of this article, but the main stack we're using are:

 + [Vagrant](https://www.vagrantup.com/) + Virtual Box for development environment
 + MySQL as database
 + [Slim](http://www.slimframework.com/) as server side language
 + [Vue.js](http://vuejs.org/) as javascript framework
 + jQuery and Bootstrap (duh!)

Being learning web development for almost 2 years, I have to say this is so far the most challenging app with great complexity I've built. So here I'm going to conclude the thing I had learn through the development. Please note that we've did not achieve everything mentioned below but if you give me time travel machine, those are the thing I would definitely do.

### 1. Do not fear to try new technology
This's it. Web development moving phase is fast. When we start our development with Vue.js the latest stable version is [0.11.10](https://github.com/yyx990803/vue/releases/tag/0.11.10). After 2 months it's now [0.12.8]([https://github.com/yyx990803/vue/releases/tag/0.12.8) with some breaking changes. 

I did try a lot of tools including Javascript build tools(more to come), unit testing tools. Most of the tools are not picked up but I learned a lot about the tools out there. 


### 2. Build Tools at Early Stage of Development
We did not use any build tools to transpiled our front end code(js, css) at the early stage of development. However as we use more and more 3rd party libraries, our code became
```html
<script src="3rd-party.js"></script>
<script src="3rd-party.js"></script>
    <!-- ... others ... -->
<script src="3rd-party.js"></script>
```

This mean that when our app starts, our server has to respond with as many as requests it's being called. We wanted to concat our code and minify it into a chunk to decrease the HTTP request called and also file size.

There are a lot of build tools but choosing the one the suite your development was hard. I tried almost every build tools I know (gulp, broccoli, jspm, webpack) just to find the loved one. And we finally opted for [laravel-elixir](https://github.com/laravel/elixir) which is built on top of gulp.

*laravel-elixir* turned out to suit our development, so we adopted it. The thing is since we do not include the raw script in out html code so we have to transpile our code for every file changes. Many're confused at first when the development way changes and the development process slow down. Thing din't work as expected because some forgot to transpile the code. So, it's best to find the build tools at the early stage of development so that everyone get used to it as fast as possible.

### 3. About Stylesheets

<blockquote class="twitter-tweet tw-align-center" lang="en"><p lang="en" dir="ltr">A preprocessor makes you write better CSS in the same way buying a better brush makes you a better artist.</p>&mdash; I Am Devloper (@iamdevloper) <a href="https://twitter.com/iamdevloper/status/483980172098826240">July 1, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

A tweet speaks a thousand of words. The reason we were not using this is probably we did not use any build tools at the early stage. Changing from plain css to sass, less is difficult, so we keep breaking the rules of *dry* in our css code.

There are a lot of great advantage about using tools like sass, less. One of the pain thing I can quickly think of in writing plain css is code reuse. Imagine that you have a theme color for your application. And so you use it everywhere.

```css
/* let's say we use #02929A */
a { color: #02929A }  /* all links color */
footer > p { color: #02929A } /* some text in text */
/* and everywhere else where you want to use this color */
```

Then one day the team decide to change the theme color and that's the nightmare. With preprocessor tools like [sass](http://sass-lang.com/)

```
$theme-color: #02929A;
a { color: $theme-color }  /* all links color */
footer > p { color: $theme-color } /* some text in text */
```
How awesome is that?

### 4. Pull Request, to merged or not to be merged ?

Every pull request matters. Treat every pull request cautiously. There are a lot of pull request I merged which made me feel guilty after that. Let people know what do you think about the pull request. Take time to discuss. If not, you'll have to come back to the code and refactor after all.

Also use CI tools like travis (we use jenkins) to run test on each pull request. We did not do this on every pull request and when I tried to run test to test out the new feature I just wrote, unrelevant test fails. We did not know which pull request failed the test and time was wasted to discover the mistery.


### 5. Testing

It may sounds silly or feeling useless to invest time to write test. But the great thing about testing is it let you write great code and you don't fear to refactor your code. There are many type of tests but let's just start with unit test. Testing like integration test is difficult. You have to deal with issues like session, authentication and database. So my advice is at least write unit test =)

![](https://cldup.com/LoHFQFDx2m.jpg)
 
Seriously, write it. Invest time, write it.


### 6. Get some life , Sleep more

This is the most important lesson I've learned. Fighting with bug all the day in front of computer isn't going to make you productive. Go do something else. Give yourself a time limit to do a thing, if it can't go well then maybe it's time for a break.
Indeed, I get most of the debugging solution, design idea when I'm not sitting in front of computer or waking up from a sleep.


## Closing

> If debugging is the process of removing bugs, then programming must be the process of putting them in. - Edsger W. Dijkstra 

When I realise there is a bug I always thought of this elegant quote. Indeed, there will be no ending in software development. As you write more feature, probably you want more (or by users, clients). 

Finally, if you think it's difficult to apply all the rules, I have only 1 request. *Always remember rule #6 !*
