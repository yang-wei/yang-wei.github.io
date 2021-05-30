+++
title = "Playing Between Unix Time and Human Readable Time in Php"
date = "2014-05-19"
slug = "2014/05/19/playing-between-unix-time-and-human-readable-time-in-php"
categories = ["PHP Unix Time"," Epoch Time"]
description = "This blog post highlights 3 PHP ways to convert human readable time to unix time."
+++
Unix time is widely used in unix-like operatin system to track down the total seconds. PHP provides some easy way to convert between Unix Time and Human Time, let's first look at the `date()` function. `date()` formats a local time/date according to its parameter format.
<!-- more -->
{% blockquote Wikipedia %}
Unix time (aka POSIX time or Epoch time), is a system for describing instants in time, defined as the number of seconds that have elapsed since 00:00:00 Coordinated Universal Time (UTC), Thursday, 1 January 1970, not counting leap second.
{% endblockquote %}

```
date( $format )
```
Example of formats     |    Example of Returned values
---  	| ---
h:i:s 	| 06:32:10
jS F Y 	| 19 May 2014
m.d.y 	| 05.19.14
DATE_RFC2822 | Mon, 19 May 2014 12:39:36 +0000



Refer to the [manual](http://jp1.php.net/manual/en/function.date.php) for more time/date format.

###Human Readable Time to Unix Time
1. Let's look at the `mktime` function.
```php
// Set our timezone
date_default_timezone_set('UTC');
// The format
mktime( $hour, $minute, $second, $month, $day, $year);
```
You can get any format
```php
echo mktime(5, 35, 0, 1, 1, 2000);	// 946704900
```
For instance, we have a string of time *010000* which indicates 1 hour. We can change this into second by using `mktime()` function like this:
```php
$string = '010000';
mktime (substr($string, 0, 2), substr($string, 2, 2), substr($string, 4, 2), 1, 1, 1970);
```
Yes, because the Unix time start from 1st of Jan, 1970 so we can convert time into seconds like this way.

2. Using DateTime object and `getTimestamp`.
Let's us see what this DateTime can do. By using `createFromFormat` instance, we can convert a very complex time format into Unix Time.
```php
//Example1
$date = DateTime::createFromFormat('m/d/y @ h:ia', '04/04/2014 @ 3.13pm')->getTimestamp();
    
//Example2
$date = DateTime::createFromFormat('YmdHis', '20130312000000')->getTimestamp();
```   

3. Of course we cannot forget this handy `strtotime`. It can understand English.
```php
echo strtotime("10 years ago"); 	// 1084971417
echo strtotime("next monday"); 		// 1401062400
echo strtotime("now");				// 1400504377
```
This is pretty straight forward comparing to other 2.

###Conclusion
This is my way to convert complex human readable time to Unix time via PHP. However, it's important to know that some of this function might fail at some point near the [year 2038](http://en.wikipedia.org/wiki/Year_2038_problem). 

###Resource
+ http://www.epochconverter.com/programming/functions-php.php
+ http://www.php.net/manual/en/ref.datetime.php
