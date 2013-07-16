---
layout: post
title: "Clojure Getting Started: Setting Up"
date: 2013-07-13 09:59
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

## Setting Up vim 

- fireplace.vim
- rainbow parents 


## Structuring Code 

- files and how they correspond to namespaces
- how to include stuff (use / require / import)



## Code is Data ([Homoiconicity](http://en.wikipedia.org/wiki/Homoiconicity)) 

You might have noticed that the parentheses are at place funny 
(in case you are used to C like languages).
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
{:a 2, :b 4}     ; a map (called hash or dict in other languages)
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

Maps can use arbitrary values as keys.
This is also valid:
```clojure
{[1 2 3] {:first "a"
          :second "b"}}
```

This is a map that uses the vector `[1 2 3]` as a key with another map
as its value. The other map uses plain keywords (`:first` and `:second`)
as keys, which have the strings `"a"` and `"b"` as their values.

clojure.org has a [cheatsheet](http://clojure.org/cheatsheet) on all the data
types and functions that create / manipulate them.



## Immutability 

Maybe you were a little surprised by the fact that every value can be used
as a key in a map. Many programming languages have restrictions on that.

Having maps itself as keys in other maps is not an issue in Clojure, 
because every data
type is immutable (once create it can not be changed).
In other languages, where this is not the case, you are restricted on the data
types you can use as keys in maps / dicts / hashes.
So how does one change things?
You don't change the thing, you just apply a function that returns a new value.

Think of it like math. If you add 1 to the number `2` you don't change the number
`2`. You apply the function + to `2` with the argument `1` and get a `3` back.
`2` will always be the value `2`.

In other words you applied a transition function (+) to go to the value `3` from
the value `2`.

Of course we also want to transition between values that are vectors or maps.
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


Now if you want to store a value globally (in the current namespace)
under a name for later referral you can use `var`s.
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


## Flow Control

Before we start writing our own functions lets add some code control tools
to our disposal.

Here is how a if / else construct looks like in Clojure:

```clojure
(if true
  "YES"
  "NO")
```

The if macro takes three arguments: the first is the condition, second is a
list that should be executed in the true case and the third is a list that should
be executed in the else case.

You probably noticed that if is not a function but a *macro*.
Remember that Clojure code is data. Basically its converted to a bunch of
lists. Now a macro is a program that is like a hook into this system.
When discovert during reading the content of if is send to the if macro, which
is a program that operates on the *code* you have written.

Optically you can't distinguish functions from macros, nor should you. Writting
macros you can use all the same tools and functions that you norally have at
your disposall, however you need to be extra carefull about naming clashes.

*Now why is if a macro and not a function?* The answer is, because all arguments
to a function are evaluated and their results are pased in as values.
Now in our case this would trigger the printing of both "YES" *and* "NO".
Since if is a macro is can decide based on the condition which of both lists
should be executed.

You see in the world of macros is very meta and there is a completly new set
of concerns when writing them. The golden rule is: always prefere writing a
function over writing a macro.

We will not cover macros in this article, because they are not part of getting
started ;). Nonetheless it's good to have a basic understanding.


## Side effects with do

That is all well and good, but what if you want to print *and* return something
in case some condition is met? That is where `do` come is.
With `do` you can group function calls:

```clojure
(if true
  (do
    (println "YES")
    "YES"))
```

This prints YES and returns a string containing "YES". Now the do is the 
only list to execute if the condition if true. `if` allows you to omit the else
case, as you probably guested.

Every time you use do you are writing a function that is not pure.
The function does things besides than constructing something to return.
`print` for example does print to the stdout and return `nil`.

If you use do you explicitly express that you do some work that
might have nothing to do with the construction of return values.

This side effects are things that actually make a program useful, but they are
also dangerous, because they manipulate a implicit global state.

Every time you find yourself writing code that is explicitly part of the thing
you are returning, you should be utterly aware what you are doing.

Code like this is common in traditional non functional programming languages,
but it's also one of the most hardest things to debug (reconstructing the 
implicit global state that let to the error).


## Functions

Until now we only used values and predefined functions.
In a real project you mainly write your own functions that take values and
combine other functions to produce new values.

In this section we will define our own recursive implementation of
the factorial function as an example.

This is how you create a function in Clojure:
```clojure
(fn [arg1 arg 2]
  (str arg1 arg2))
```

This will define a anonymous function. We could now combine this with a var
to reference it later:

```clojure
(def my-fn (fn [arg1 arg2]
             (str arg1 arg2)))
```

Because this is a very common task to do - clojure gives you `defn`, which does
exactly the same while being more concise.
```clojure
(defn my-fn [arg1 arg2]
  (str arg1 " " arg2))
```

You can call this function the same way you would any other function:
```clojure
(my-fn "hello" "world")
```

### The same Function with Different Arity 

Your can define functions that are defined for different arities and execute
different code.

```clojure
(defn greet 
  ([name]
    (str name "says: hello"))
  ([name greeting]
    (str name "says: " greeting)))

(greet "Jimmy")
(greet "Jimmy" "Wilkommen!")
```

### Tail Recursion

We all learned writing recursive code - while elegant - can also blow
your stack if it's nested to deeply.
When you write your code in a way, that has no dependency on the previous 
stack frame, the compile can apply [tail recursion](http://c2.com/cgi/wiki?TailRecursion)
which means it has no need to store the old stack frame or create a new one.

This enables space unlimited recursion depth, so you don't need to worry that
you will blow your stack. However endless recursions will run forever :) .

To use tail recursion in Clojure you use the `recur` function, which calls
the same function, with the same arity.

Now we have all the tools we need to write our factorial function:

```clojure
(defn !
  ([n]
   (! 0 n))
  ([sum n]
   (if (zero? n)
     sum
     (recur (+ sum n)
            (dec n)))))
```

You see we use all the constructs we have learned so far here.
If called with one argument we call the same function giving it a initial sum
value of 0.
The `(if (zero? n))` checks for our cancellation condition, if false we
do a tail recursion passing it the new sum and a decreased value of n.



## Testing


## Code Convention

- don't put ) into new lines
- use 2 spaces
- allign arguments visually


