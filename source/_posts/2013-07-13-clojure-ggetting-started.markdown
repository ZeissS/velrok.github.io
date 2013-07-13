---
layout: post
title: "Clojure Getting Started"
date: 2013-07-13 09:59
comments: true
categories: clojure programming tutorial
---


## Setting Up a Project 

Clojure runs on the JVM so you need a recent [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version installed
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

This will create a new fold. For the rest of this writing all file paths will 
be relative to this folder.


You should see a ***project.clj*** file looking something like this

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

## Code is Data ([Homoiconicity](http://en.wikipedia.org/wiki/Homoiconicity)) 

You might have noticed that the parentheses are at place funny 
(in case you are use to C like languages).
This is because Clojure is homoiconic, which is to say that code is data.
You are basically programming in Clojures [AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree).
Clojure is a dialect of [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)), 
so you write programs in the form of lists.

The reader will read the string / file content and convert it so a list.
During the execution the first entry in a list is interpreted as a function,
while all the following elements are assumed to be arguments to that function.

```clojure
(func arg1 arg2)
```

So a nested function call looks like this:

```clojure
(+ (* 2 3)
   (/ 20 5))
```
This will calculate `(2 * 3) + (20 / 5)`.
Expressing math in Clojure always takes the form of prefix notation.

So what if you just want to create a plain list?

The answer is: normally you don't. You use vectors instead, because they have
similar properties and provide a literal representation:
```clojure
[1 2 3]
```
But if you really want a list you can use:
```clojure
; the list function that converts its arguments into a list
(list 1 2 3)
; or you can quote the expression, telling the executer not to
; interpred the conent as a function.
(quote (1 2 3))
```

## Basic Data Types

Clojure follows the idiom that having many functions on a few data structures
is better to have a few functions on many data structures.
Which means the default data types are all you should need.

Here is a listing of all the basic data types in Clojure.

```clojure
1   ; int
1.0 ; float
1/2 ; ratio, arbitray precision

"hello world" ; string
:hello        ; keyword (like symbols in ruby)
#".*"         ; regular expression

(list (1 2 3))   ; single linked list
[1 2 3]          ; vector (constant random access)
{:a 2, :b 4}     ; a map (called hash or dic in other languages)
                 ; a succession of key value pairs. You an skipp the , if you like
#{1 2 3}         ; a set
```

Ratio is a Clojure specific data type that can hold ratios without the loss of
precision. If you use them in calculations they will cancel to the lowest possible
denominator.
You can convert them to floating point by calling `(float 1/2)`. Keep in mind
that by doing so you probably loose precision.

Keywords always evaluate to them self. Like the literal 1 stands for the
value one being only present once, the keyword :key stands for the unique
sequence of this three letters `k` `e` and `y` .
They are used as what their name suggests: keywords ;) .
I hope this is somewhat clear, it's always tricky to explain this kind of things.

Maps can use arbitrary vales as keys.
This is also valid:
```clojure
{[1 2 3] {:first "a"
          :second "b"}}
```

clojure.org has a [cheatsheet](http://clojure.org/cheatsheet) on all the data
types and functions that create / manipulate them.



## Immutability 

Maybe you were a little surprised by the fact that every value can be used
as a key in a map. Many programming languages have restrictions on that.

The reason why this is not an issue in Clojure, is that in Clojure every data
type is immutable (once create it can not be changed).
So how does one change things?
You don't change the thing, you just apply a function that returns a new value.

Think of it like math. If you add 1 to the number 2 you don't change the number
2. You apply the function + to 2 with the argument 1 and get a 3 back.
2 will always be the value 2.

In other words you applied a transition function (+) to go to the value 3 from
the value 2.

Of coarse we also want to transition between values that are vectors or maps.
The [cheatsheet](http://clojure.org/cheatsheet) has a list of all the functions
you may want to apply to the different data types.
Here we will cover the most basic functions:

```clojure
; vectors
(conj [1 2] 3)
; -> [1 2 3]
(assoc [1 2] 0 :a)
; -> [:a 2]
(get [:a 2] 0)
; -> :a

; maps
(assoc {:a 1 :b 2} :a 5)
; -> {:a 5 :b 2}

(dissoc {:a 1 :b 2} :a)
; -> {:b 2} 

(keys {:a 1 :b 2})
; -> (:a :b)
(vals {:a 1 :b 2})
; -> (1 2)
```


## Vars

Now if you want to store a value under a name for later use you can use `var`s.
A `var` is a data type in clojure that points to a value.

You can define a var like this:

```clojure
(def x 2)
```
To be precise this will create a var, that points to the value 1, 
and assign it to the symbol `x` (in the current namespace).

If you now run:
```clojure
(println x)
; 2
; -> nil
```
Everything you write that is not a data type literal is a symbol that stands
for something. `println` is a symbol that stands for the function that prints
a line to stdout and ends with a new line.

If you just enter:
```clojure
println
; -> #<core$println clojure.core$println@19b8db54>
```
You will get the actual function.





## Setting Up you Editor 

### vim

- fireplace.vim
- rainbow parens

## Structuring Code 

- files and how they correspond to namespaces
- how to include stuff (use / require / import)


## Functions

- defining functions
- functions with different arity
- anonymus functions


## Controll Flow

- if
- case 

## Code Convention

- don't put ) into new lines
- use 2 spaces
- allign arguments visually

## Code is Data ([Homoiconicity](http://en.wikipedia.org/wiki/Homoiconicity) ) 





<!-- more -->
<div id="disqus_thread"></div>
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
    var disqus_shortname = 'meinsinn'; // required: replace example with your forum shortname

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<a href="http://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>
