+++
title = "Update Jvm Version in Mac"
date = "2015-10-01"
slug = "2015/10/01/update-jvm-version-in-mac"
Categories = ["Java", "JVM"]
description = "Update Java in Mac OS"
+++

Being a newbie for Clojure, the problem I met when trying to use its other core library - `clojure.core.reducers` is the "namespace not found...".
```clojure
(require '[clojure.core.reducers :as r])
```
Get this:
> clojure.lang.Compiler$CompilerException: java.lang.Exception: namespace 'clojure.core.reducers' not found, compiling:
> ...
> ...
> ...
> Caused by: java.lang.Exception: namespace 'clojure.core.reducers' not found
<!-- more -->
From the docs,
```clojure
(ns ^{:doc
      "A library for reduction and parallel folding. Alpha and subject
      to change.  Note that fold and its derivatives require Java 7+ or
      Java 6 + jsr166y.jar for fork/join support. See Clojure's pom.xml for the
      dependency info."
      :author "Rich Hickey"}
  clojure.core.reducers
  (:refer-clojure :exclude [reduce map mapcat filter remove take take-while drop flatten cat])
  (:require [clojure.walk :as walk]))
```

It seems like we need Java version that support fork/join operation. Let's see 
what version do we have?

```
❯ java -version
java version "1.6.0_65"
Java(TM) SE Runtime Environment (build 1.6.0_65-b14-466.1-11M4716)
Java HotSpot(TM) 64-Bit Server VM (build 20.65-b04-466.1, mixed mode)
```
Our system show we're using Java 6 which does not meet the requirement for `clojure.core.reducers`. We need a to upgrade our JDK.  Coming from non Java background, I do not even know the JRE or JDK thing. But in this case what we need is for development, so it makes sense to just download the JDK. So let's download and install it from the [official page](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Once we finished installation, the latest version is loaded default.
```
❯ java -version
java version "1.8.0_60"
Java(TM) SE Runtime Environment (build 1.8.0_60-b27)
Java HotSpot(TM) 64-Bit Server VM (build 25.60-b23, mixed mode)
```

If you start your repl again, you will see it's using the latest verison.
```
~ ❯ lein repl
nREPL server started on port 55481 on host 127.0.0.1 - nrepl://127.0.0.1:55481
REPL-y 0.3.5, nREPL 0.2.6
Clojure 1.6.0
Java HotSpot(TM) 64-Bit Server VM 1.8.0_60-b27
```

Ok so we are cool now:
```
(require '[clojure.core.reducers :as r]) // nil
```
