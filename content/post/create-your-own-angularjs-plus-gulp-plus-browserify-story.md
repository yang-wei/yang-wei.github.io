+++
title = "Create Your Own Angularjs Plus Gulp Plus Browserify Story"
date = "2014-10-16"
slug = "2014/10/16/create-your-own-angularjs-plus-gulp-plus-browserify-story"
categories = []
description = ""
published = false
+++
When I want to start an angularjs, I always did something like this.
```
yo angular
// Did all awesome thing
```
I know [Yeoman](yeoman.io) is a great scaffolding tool. It helps me to kickstart new project so quickly and I totally love it.
There is just one problem, it did too much thing. Something that is not needed in my project, or something that I don't even know.
So I decided to build my own *ng-boilerplate*.

###Tools
I decided to opt for the tool I never used before(gulpjs, browserify) this time. So here's what they do.
 + npm - install local dependencies such as gulp, bower and also gulp plugin etc
 + bower - install local dependencies for frontend such as angular, jquery, foundation etc
 + browserify -
 + git -

If you do not have npm, bower and git, you should install them first.
Nowadays, npm can used to install [angular](https://www.npmjs.org/package/angular) but I decided to manage all my frontend dependencies using [bower](bower.io). 
If you're confused what's the difference like me before I wrote this post, bower keep all its dependencies uses *flat dependency tree* which doesn't allow you to
different version of dependency(jQuery 1.1, jQuery 1.9) while npm allows you to do that. We probably don't want to have different version of same dependency on our
client side because it slows downn our app.

###Init everything
We will start building our boilerplate now.
```
mkdir my-boilerplate
npm init        // create package.json
bower init      // create bower.json
git init       // create .git    
```
Noticed that there will be questions prompted out during the init process, you can select default for all unless the *main*. We will be using a single module
(in our case - gulpfile.js) to serve as our entry point so just update the *main* field to *gulpfile.js*. 

Also we want bower dependencies to be installed within our
angular *app* folder, so we have to add `.bowerrc` in our root. Inside our `.bowerrc`,
```
{ 
    "directory": "app/bower_components"
}
```
Now I know what does this `.bowerrc` does. Awesome ! Ok, now let's create our `.gitignore` file to ignore some directory. Inside `.gitignore`,
```
node_modules
app/bower_components
*.swp
*.DS_Store
```
So let's get our angularjs via bower and gulp via npm.
```
bower install angular angular-ui-router foundation --save
npm install gulp --save
```
