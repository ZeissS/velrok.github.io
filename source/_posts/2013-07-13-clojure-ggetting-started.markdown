---
layout: post
title: "Clojure Getting Started: Setting Up"
date: 2013-07-17 00:10
comments: true
categories: clojure programming tutorial
---


## Setting Up a Project 

Clojure runs on the JVM so you need a recent 
[JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 
version installed
(Java 6 or newer should do).

To create a new Clojure project we use [Leiningen](https://github.com/technomancy/leiningen).
On Mac OS it's just a

```
brew install leiningen
```
away.

This will install Leiningen 2.
Version 2 breaks compatibility with the old plugin system, but most projects
that supply Leiningen have a description how to add them to your project.clj
for each version.
However if you start a new project, always go with Leiningen 2.

To create our getting started project (we will name it greenfield-clojure) run:
```
lein new greenfield-clojure
```

This will create a new folder. For the rest of this writing all file paths will 
be relative to this folder.


You should see a `project.clj` file looking something like this

```clojure
(defproject greenfield-clojure "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.5.1"]])
```
As you can see Clojure itself is defined as a dependency.
As of this writing the latest version is 1.5.1 .

Now run
```bash
lein deps
```
which will download and install all specified dependencies.


During this project we will add additional dependencies to this project.
[CloJars](https://clojars.org/clj-http) is a repository for Clojure libaries.
There you can find a lot of interesting stuff.
You can also include Java dependencies from [Maven Central](http://search.maven.org/).
Please consult the Leiningen [sample.project.clj](https://github.com/technomancy/leiningen/blob/stable/sample.project.clj)
for further information.

<!-- more -->

## The REPL

The REPL is a interactive environment where you can run Clojure code, in the
context of your project.

To start a REPL run:
```
lein repl
```
This will also download and install all dependencies that are given in the
project.clj.

Now we can start and enter a simple **hello world** into the repl:

```clojure
(println "Hello world!")
```

## Setting Up Your Editor

### Sublime Text 2

I started out, using Sublime Text 2.
It's a good start. I can recommend installing [SublimeREPL](https://github.com/wuub/SublimeREPL).
Clojure development uses the REPL extensively, so your editor should
come with some kind of REPL integration.

I found SublimeREPL to be not as stable as I would have liked.
So I switched to vim.

Of course on of the best editors for Clojure is Emacs, but I just can't get
used to it :). All Emacs folks out there: please leave plugin recommendations
in the comments, I will integrate them.

### vim 

As mentioned above I switched from Sublime to vim, because its REPL integration
using the [fireplace.vim](https://github.com/tpope/vim-fireplace) plugin,
proved to be more stable.

Once your setup is complete you will be able to send the complete file or
complete calls under the cursor to the REPL by pressing a key or a combination
of keys.
Make sure this works we will use it in the future of this tutorial.
You can of course just continuously copy and paste the snippets to the REPL by
hand. However that is very tedious and time consuming.

You want to use the REPL, because restating the JVM just to execute one file
takes seconds, which makes for a very frustrating change, feedback cycle.

While you are at it you might also want to install 
[rainbow_parentheses.vim](https://github.com/kien/rainbow_parentheses.vim).
It will highlight matching parenthesis in the same color, while changing the
color with each pair.

## Structuring Code 


### Files and Namespaces

If you follows the tutorial and have created the *greenfeld_project* you will
find a file called `src/greenfeld_project/core.clj`.
This is the default starting point.

The first line of the file should look like this:

```clojure
(ns greenfield-clojure.core)
```

`ns` creates the new *namespace* `greenfeld-clojure`.
Namespaces in Clojure have to respond to the file structure.

Given a namespace `greenfield-clojure.domain.user` it will look for a file in 
`src/greenfeld_clojure/domain/user.clj`.
Notice that it uses `_` for file names and `-` for the namespaces.

Lets create a new clj file:

```
touch src/greenfield_clojure/util.clj
```

Following the naming schema we start a fitting namespace for that file by
adding:

```clojure
(ns greenfield-clojure.util)
```

All the following definitions will go into that namespace.

Now lets add a simple greeting function:

```clojure
(ns greenfield-clojure.util)

(defn greet [name]
  (str "Welcome, " name "!"))
```

In the same namespace you can make a test call: `(greet "Jane")`.

If you have your REPL going just put the code below the function definition
itself and send it to the REPL for evaluation.
You can remove the code afterwards.

This way you can quickly setch together some code and move and refactor later.

If you use the REPL via command line you can switch to this (or any other valid
namespace) by calling:

```
(ns greenfield-clojure.util)
```

### Using Code from other Namespaces

Obviously we don't just want to create namespaces we want to use stuff that is
located in other namespaces. We can do so by useing `use` and / or `require`.


#### use

`use` will include all the vars (function definitions are just vars that point
to functions) into the current namespace.
This will NOT pull vars the the used namespace itself includes via `use`.
None the less: this polutes you namespace and it gets harder to avoid clashes
and find where functions are defined.
However if you want to use for example clojure core namespaces it sometimes
makes for nicer code.

Here we include clojure set operations with the `use` function:

```clojure
(use 'clojure.set)
(intersection #{1 2 3} #{3 4 5})
```

Notice that we need to quote the namespace.

This is usefull when working in the REPL itself, but when you write files it's
better to include this in the `ns` call directly.

`ns` lets you specify lists that start with keywords, which it than handles.
We can write the same use `ns`:

```clojure
(ns greenfield-clojure.util
  (:use [clojure.set]))

(intersection #{1 2 3} #{3 4 5})
```
If we only want to use the intersection function from the set namespace
we can change the code to:

```clojure
(ns greenfield-clojure.util
  (:use [clojure.set :only [intersection]]))
```

It's good pracice to use `ns` variant. The `use` function is really only for
the REPL.

#### require

We have seen that `use`, while nice in some cases, polutes our namespace.

Instead we use `require`. Let us require our util namespace from the core
namespace:

```clojure
(ns greenfield-clojure.core)
(require '(greenfield-clojure [util :as util]))
```

Now this is very cumbersome and we already established that we prevere the `ns`
version of things. So lets change it:

```clojure
(ns greenfield-clojure.core
  (:require [greenfield-clojure.util :as util]))
```

You can of course both use and require namespace at once:

```clojure
(ns greenfield-clojure.core
  (:use [clojure.set])
  (:require [greenfield-clojure.util :as util]))
```

And here is a version that uses and requires multiple namespaces:

```clojure
(ns greenfield-clojure.core
  (:use [clojure.set]
        [clojure.java.io :only [file]])
  (:require [greenfield-clojure.util :as util]
            [clojure.string :as string]))
```

With our utils library requires we can call it like this:

```clojure
(util/greet "Xavier")
```

In clojure the `/` to name vars from a different namespace.


## Executing from the Command Line

Util now we only executed our code using a REPL. But at some point in time
we will want to start our program from the command line.

There are two ways to do this:

1. use leiningen
1. package everything up into one big jar file

In any case our main namespace must contain a `-main` function that takes
a variable number of arguments:

```clojure
(defn -main[& args]
  (println (util/greet "Xavier")))
```

This will just always print the same greeting.


### Running with Leiningen

Using leiningen we can call:

```
lein run -m greenfield-clojure.core
```

`-m` tells Leiningen wich namespace to use for execution.
The namespace must include a `-main` function.
*Notice the `-` before the main*.

We can also specify the main namespace in our `project.clj`:

```clojure
(defproject greenfield-clojure "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :main greenfield-clojure.core
  :dependencies [[org.clojure/clojure "1.4.0"]])
```

Now we can ommit the `-m ...` part:

```
lein run
```

### Creating an Uberjar

We can use Leiningen to package everything up into one big jar that can be
executes very simply.

For that we have to set the main namespace (same thing we did above):

```clojure
(defproject greenfield-clojure "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :main greenfield-clojure.core
  :dependencies [[org.clojure/clojure "1.4.0"]])
```

But because this will be called directly, we have to tell clojure that it should
compile the clojure code into a java class when creating the jar.
To do this add a `(:gen-class)` argument to the `ns` call:

```clojure
(ns greenfield-clojure.core
  (:require [greenfield-clojure.util :as util])
  (:gen-class))
```

This is also call AOT ( Ahead of Time Compilation).
It's sufficient to do this for the main namespace only.
You should be carefull with this. In case implementations of core clojure
functionallity changes your ATO compiles code might not be compatible without a
renewed compilation.


A call to

```
lein uberjar
```
will create a standalone jar in `target`

Run it via
```
java -jar target/greenfield-clojure-0.1.0-SNAPSHOT-standalone.jar
```

## Summery


We have done it!

In this tutorial we:

1. setup and installed clojure from scratch
1. fired up a REPL for interactive development
1. configured our favorit editor (if it is Sublime or vim ;) )
1. leared how the file structure matches up to namespaces
1. how to create our own namespace
1. how to include code from other namespaces
1. we used leiningen to run our code from the command line
1. bundled everything up in one nice care-free jar that can be pushed around the computing world

I hope this tutorial was helpfull or interessting.

Any feedback is much appreciated.
