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
- some java interop (Parallel Colt, Mahout)
- simple data analysis (incanter)


## likes

- immutable data structures
- awesome that they are persistent (include link to hicket and guru talk)
- syntax flexability
- REPL


## dislike

- debugging :(
- error messages
- suspission: (partial *)
- JVM startuptime xD


## lessons learned

- thinking with immutable data structures in very different. You will need some time.
- don't use (use)
	- use (require) instead
- inject state via function arguments, done this right :)
- link sierra talk
- don't overuse ->> let is ok in many cases
- Write test!! despite the REPL (was a bit to lazy in that regard)
- prallel pmap does the trick in many cases
	- start at top level
- want to experiment more with reducers!
- contracts are amazing! {:pre :post}
- no need for vars really!
- they introduce global state, so use with utmost caution