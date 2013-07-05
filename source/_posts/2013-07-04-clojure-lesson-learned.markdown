---
layout: post
title: "Clojure Lesson Learned"
date: 2013-07-04 18:45
comments: true
categories: 
---

## background

- new to clojure
- working with it for about 2 Month now
- some java interop with ([Parallel Colt](http://incanter.org/docs/parallelcolt/api/), [Mahout](http://mahout.apache.org/))
- simple data analysis [incanter](http://incanter.org/)


## likes

- immutable data structures
- awesome that they are persistent
<iframe width="420" height="315" src="//www.youtube.com/embed/wASCH_gPnDw" frameborder="0" allowfullscreen></iframe>
- syntax flexability
- REPL


## dislike

- debugging :(
- error messages
- suspission: [partial](http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/partial)
- JVM startuptime xD


## lessons learned

- thinking with immutable data structures in very different. You will need some time.
- don't overuse [use](http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/use)
	- use [require](http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/require) instead
- inject state via function arguments, done this one right, see [Stuart Sierrra: Clojure in the Large](http://www.infoq.com/presentations/Clojure-Large-scale-patterns-techniques)
- don't overuse [->>](http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/->>) [let](http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/let) is ok in many cases
- Write test!! despite the REPL (was a bit to lazy in that regard)
	- The default Test framework seams to be [midje](https://github.com/marick/Midje)
	- but I liked my classical TDD/BDD DSL so I used [speclj](http://speclj.com/)
- prallel [pmap](http://clojuredocs.org/clojure_core/clojure.core/pmap) does the trick in many cases
	- start at top level
- want to experiment more with [reducers](http://clojure.com/blog/2012/05/08/reducers-a-library-and-model-for-collection-processing.html)!
- [pre and post assertions](http://blog.fogus.me/2009/12/21/clojures-pre-and-post/) are a blessing and so easy to use
- you need only a hand full of vars. In many cases none at all. [Ask Stuart](http://www.infoq.com/presentations/Concurrency-Clojure) ;) 
- they introduce global state, so use with utmost caution
- I can't remember the syntax for optional parameters!
- [print.foo](https://github.com/AlexBaranosky/print-foo) is nice if you have small data


## vim and clojure

- vim fireplace REPL more stable that sublime REPL
- Sublime Repl nicer (because it shows the last call)
- vim fireplace hace invaluable :Doc and :Souce commands!
- no ctags support for clojure but lisp works fine
- rainbow parentecies
- `y%`and `d%` are big time and brain power savers