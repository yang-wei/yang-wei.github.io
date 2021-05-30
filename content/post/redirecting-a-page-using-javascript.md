+++
title = "Redirecting a Page Using Javascript"
date = "2015-09-22"
slug = "2015/09/22/redirecting-a-page-using-javascript"
categories = ["redirect", "client-side"]
description = "A blog post that shows how to do redirect using JavaScript or even jQuery"
+++

If you are writing web app using JavaScript heavily, you might need to do redirect on the browser.

#### `window.location` object

One of the method we can refer to is the global `location` object. It does provide some method for us to do the redirect.
<!-- more -->
```js
location.replace("http://google.com");
location.assign("http://google.com");
```
Or we can assign the url directly to the object.
```
location = "http://google.com"
// more explicit way
location.href = "http://google.com";
```
If you want a more explicit way:
```
window.location.href = "http://google.com";
```

Open developer tool and try any of these in the console, you will be redirect, good bye!

(welcome back if you come back from redirect page)

#### What about jQuery ?
Indeed we can redirect using plain JavaScript, we don't even need jQuery. However in case you(or your team) insist to write code jQuery-ish, then this willl do the work:
```
$(location).attr('href','http://www.google.com')
$(window).attr('location','http://www.google.com')
$(location).prop('href', 'http://www.google.com')
```
Basically what this does is just take a DOM object and inject a new value to it. So if you know the plain JavaScript way this seems natural to you.

#### Extra: Reload Page
In some case you might want to force your page to refresh to get latest UI update or anything from server. In that case `location.reload` would be useful.

```
location.reload(true);
```

Further reading:

 * [MDN page](https://developer.mozilla.org/en-US/docs/Web/API/Window/location)
