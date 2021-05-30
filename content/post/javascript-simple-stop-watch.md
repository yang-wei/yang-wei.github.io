+++
title = "Javascript Simple Stop Watch"
date = "2014-07-20"
slug = "2014/07/20/javascript-simple-stop-watch"
categories = ["JavaScript"]

+++
In this post, we'll see how to create a simple 3 minutes stop watch application by Javascript. Time will be counted by using `setInterval()` function. Nothing difficult here, let's first looks at HTML first.
<!-- more -->
```
<h3>3 Minutes Stop Watch</h3>

<p> 
	<span id="min">0</span>min
	<span id="sec">0</span>sec
</p>
<p>
    <button id="start">Start</button>
    <button id="stop" disabled>Stop</button>
    <button id="reset">Reset</button>
</p>
```
Text for showing remaining minutes and seconds are created in the `<span>` tag. This stopwatch contains 3 buttons which are *start*, *stop*, *reset*. We will use `addEventLister()` to trigger our function when any of these button is clicked. 
```javascript
start = document.getElementById("start"),
start.addEventListener("click", function(){
	// start counting
	})
```
Stop and reset will be set similarly. Next, in our HTML we can see the *stop* button is being disabled. However, when we click *start* we want it *start* to be disabled and *stop* to be enabled. So we'll create a function called `trigger()` to do this. 
```
function trigger(){
	if(start.disabled) {
		/* start button will be disabled once it's clicked */
		start.disabled = false;
		stop.disabled = true;
	} else {
		start.disabled = true;
		stop.disabled = false;
	}
};
```
Next we'll code our function for counting down the time. 
```
function count() {
	if (time === 0) {
		// all showing 0
		// interval will be cleared
	} else {
		// counting down time
		var time = time - 1;
		var second = time % 60;
		var minute = Math.floor(time / 60);
	}
};
```
Our stop watch application is almost done after the `trigger()` and `count()` function is done. I included a jsfiddle below to show the working application.

{% jsfiddle HF5Yf/3/ %}

Obviously I'm not good at design. Just make sure it works.

{% blockquote Michael Sinz %}
Programming is like sex: one mistake and you have to support it for the rest of your life.
{% endblockquote %}

