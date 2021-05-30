+++
title = "Moving React Component to Server Side"
date = "2014-12-14"
slug = "2014/12/14/moving-react-component-to-server-side"
Categories = ["react"," isomorphic"," react server side"]
description = "Render React on server side the isomorphic way"
+++

###React Server Side Rendering Example
I had a hard time doing this, the documentation(currently __*v0.12.1*__) doesn't provide enough information regarding to server side rendering at the time this post was writting. Most tutorials are either outdated or din't really focus on this topic. Also, there are too many tools used in most examples that beginner like me will hardly understand. So I decided to write down what I had learn by and explain the tools needed to render React components on server side after struggling long enough to make it works. 
<!-- more -->
####Prerequisite
To follow this post smoothly, you will need
 * Node (using *v0.10.33*)
 * npm (using *2.1.10*)
 * Read through [getting-started](http://facebook.github.io/react/docs/getting-started.html) (using *v0.12.1*) on React documentation

We will take the [example](http://facebook.github.io/react/docs/thinking-in-react.html) from *Thinking in React* section on [React](http://facebook.github.io/react) documentation. In the end we will see how to make this component
 
 * rendered on server side 
 * working on client side as well

Let's begin by setting up our `package.json`.
```
{
  "name": "TDDReact",
  "scripts": {
    "build": "browserify -t reactify app.js -o public/bundle.js"
  },
  "devDependencies": {
    "browserify": "^7.0.0",
    "reactify": "^0.17.1"
  },
  "dependencies": {
    "express": "^4.10.6",
    "node-jsx": "^0.12.4",
    "react": "^0.12.1"
  }
}
```
I try to decrease the dependency in order to achieve simplicity. For now please ignore the *script* field and other dependencies other than express. Run `npm install` and all dependencies should be installed. We will start by setting up express server. At first, `server.js` might looks like this.
```javascript
var express = require('express');
var app = express();

app.get('/', function(req, res) {
    res.send('Hello React');
});

app.listen(9999);
```
Our server should be working if you run `node server.js`. Point browser to `localhost:9999` and everything should be working. 
locNext, let's grab the code from React documentation, the one we need and we will put in our `components/filterableProductTable.js`. It looks like this.

{% jsfiddle w1t7upmp js,result %}

That's a little thing we need to change in order to make this work on server. We will create another file name `app.js` in our root directory. This file will be used to render the whole components on both server and client side. We will see how to do that in a moment. Also we will pass in the data(in this case the `PRODUCTS`) from `app.js`.
```javascript
var React = require('react');
var Table = require('./components/filterableProductTable.js'); 

// move data here
var PRODUCTS = [ ...  ];

var APP = React.createClass({
  render: function() {
    return (
      <html>
        <head>
          <title>React Server Rendering</title>
        </head>
        <body>
          <Table products={PRODUCTS} />
        </body>
      </html>
    );
  }
});

module.exports = APP;
```
Here we are using *React* npm tools. You see we require component from our `filterableProductTable.js` so we have to exports module from it.
```javascript
var React = require('react');   //              <-- add this on top
...
// remove PRODUCTS and the render method
...
module.exports = FilterableProductTable;        <-- add this on bottom
```
Ok need more little works on `server.js`. Because we use *JSX*, so we have to transform it to javascript in order to make it work. This is where `node-jsx` module comes in. By `require('node-jsx').install()` in our `app.js`, it automagically transform our JSX ( If you are writing React without JSX you can skip this step). Then this time when we request, we want to response with React componenets instead of Hello React!. Here, we can use `React.renderToString() ` method.
```javascript
require('node-jsx').install();              // <-- transform JSX to javascript
var React = require('react');
var APP = require('./app');                 // <-- require our main component 

var express = require('express');
var app = express();

app.get('/', function(req, res) {
  var markup = React.renderToString(APP());  // <-- render the APP here
  res.send(markup);                          // <-- response with the component
});
app.listen(9999);
```
If we go to the browser this time, we will see the component is rendered. But wait, isn't that not working properly ??? If you open your developer tool you won't see any javascript, this is because we are just rendering static thing.

###So How??
First of all when we send the html fragment on our `app.js`, we have to insert a bundle script which contains javascript for the client side. So how do we do this ? The answer is [Browserify](http://browserify.org/). In case you never use it, it's a tool that will bundle up all our dependencies called by `require()` and make it work in browser. Maybe you had notice we also had a dependency named *reactify* in `package.json`, similar to *node-jsx*, it's used to transform our jsx to javascript which works nicely with Browserify. Let's make a directory called `public` where we want our client side javascript(`bundle.js`) be after it's browserified and reactified. We can make all of this work through one line from command line.
```
browserify --transform reactify app.js --outfile public/bundle.js
```
We can make use of `package.json` script field like above, so that we only have to hit `npm run build` to trigger this command. 

Then in our `app.js` html fragment, we can insert this `bundle.js`. 
```html
<script src="public/bundle.js></script>
```

Also we want to tell React that 'Hey React, if we are in browser, please render like you always do in client side'. 
```js
// app.js
if(typeof window !== 'undefined') {
  window.onload = function() {
    React.render(APP(), document);
  }
}
```
A very important note for here is we have to make sure we give React the same code to what it's rendered on server. If we try to render it inside of `document.body` instead of `document`, probably this won't work and then you will get an error like *React attempted to use reuse markup in a container but the checksum was invalid* later. Keep in mind to make sure that both markups generated by client and server side are the same.

Finally we want to serve static file in our express server. This can be easily done by adding this to `server.js`.
```
app.use('/public', express.static(__dirname + '/public'));
```

This time our React code will be working perfectly. Checkout the final source code ot [Github](https://github.com/yang-wei/react-server-rendering.git), clone it and play with it.

###Final thought
I hope this can save you time to figure out how to render React components on server side. Of course you can use other tools other than what I used here, for instance [webpack](http://webpack.github.io/) instead of Browserify, or using [React-tools](https://www.npmjs.org/package/react-tools) to transform the JSX.

