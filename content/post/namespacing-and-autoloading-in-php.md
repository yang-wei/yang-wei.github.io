+++
title = "Namespacing and Autoloading in Php"
date = "2015-03-09"
slug = "2015/03/09/namespacing-and-autoloading-in-php"
categories = ["namespace"," composer autoloading"," PHP"," oop"]
description = "namespacing and autoloading by composer"
+++
Recently, I have to revisit PHP because of a new project and I realized that `namespace` is totally new for me. So I thought it'll be good to write down a quick note. This post will go quickly on how namespacing and autoloading work in PHP by using Composer.

<!-- more -->
###Our Application
Our application is just a simple page echoing some text using `json_encode` method.
Let's create a `index.php` and write all our codes inside.

```php
<?php

class Team {
  protected $members = [];
  public $name;

  public function __construct($name, $members = [])
  {
    $this->name = $name;
    $this->members = $members;
  }

  public function add(Person $person)
  {
    $this->members[] = $person;
  }

  public function members()
  {
    return $this->members;
  }
}

class Person {

  public $name, $role;

  public function __construct($name, $role)
  {
    $this->name = $name;
    $this->role = $role;
  }
}

$kobebryant = new Person('Kobe Bryant', 'player');
$Lakers = new Team('Lakers', [$kobebryant] );

// add player and coach
$jeremylin = new Person('Jeremy Lin', 'player');
$Lakers->add($jeremylin);

$byronscott = new Person('Byron Scott', 'coach');
$Lakers->add($byronscott);

// output
echo(json_encode($Lakers->members()));
```
If we execute this file (either start a server by `php -s localhost:8000` or `php index.php`), we will see an array with 3 objects inside.
```js
[
  {"name":"Kobe Bryant","role":"player"},
  {"name":"Jeremy Lin","role":"player"},
  {"name":"Byron Scott","role":"coach"}
]
```
Cool app right? Let's explore namespacing and composer autoloading by rewritting this app.

###Breaking Down Classes
If we keep expanding our application, this `index.php` will become very large. This makes our code hard to read and maintain. So let's give each class their own files located inside `src/` directory.

```
├── index.php
└── src
    ├── Person.php
    └── Team.php
```

PHP won't know the files inside `src/` directory if we do not tell it. There are a few methods to do so, one of them is we can use `require` method to load the needed files but there's a better way to solve this - [composer](https://getcomposer.org/) autoloading. (I assume composer is installed on your system). First to initialize composer, all we need is `composer.json`.

```js
//composer.json
{}
```

Put this on project root and run `composer install` will create a new directory - `vendor`. This is the place where composer stores our project autoload configuration and dependencies. To make use of composer autoload, we'll modify our `composer.json`.

```
{
  "autoload": {
    "psr-4": {
      "NBA\\": "src"
    }
  }
}
```
Here, composer will register a [PSR-4](http://www.php-fig.org/psr/psr-4/) autoloader for *NBA* namespace (can be any name related to our app). Now our new namespace is mapped to `src/` directory.

###Adding Namespaces

In our codes containing classes, let's give them their own namespace.
```php
// src/Person.php
<?php

namespace NBA\Person
...

// src/Team.php
<?php

namespace NBA\Team
...
```
Noticed in PHP, namespaces are required to named relatively to their paths. If `Person.php` is located inside `src/other/Person.php`, we do this:

```
namespace NBA\other\Person
```
### Autoloading

In our `index.php`, we need to load `autoload.php` provided from composer. Also to use the namespace we just declared, we use the keyword `use`.
Let's break our `index.php` down to separate some concerns. Our application logic will be placed in `app.php`.
```php
//index.php
require 'vendor/autoload.php';
require 'app.php';
```
```php
// app.php
<?php

use NBA\Person;
use NBA\Team;

$kobebryant = new Person('Kobe Bryant', 'player');
$Lakers = new Team('Lakers', [$kobebryant] );

// add player and coach
$jeremylin = new Person('Jeremy Lin', 'player');
$Lakers->add($jeremylin);

$byronscott = new Person('Byron Scott', 'coach');
$Lakers->add($byronscott);

// output
echo(json_encode($Lakers->members()));
```
By executing `php index.php`, we should get the same results.
```js
[
  {"name":"Kobe Bryant","role":"player"},
  {"name":"Jeremy Lin","role":"player"},
  {"name":"Byron Scott","role":"coach"}
]
```

###Resources
 + https://getcomposer.org/doc/01-basic-usage.md
