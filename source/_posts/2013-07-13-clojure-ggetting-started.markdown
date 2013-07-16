---
## Setting Up vim 

- fireplace.vim
- rainbow parents 


## Structuring Code 

- files and how they correspond to namespaces
- how to include stuff (use / require / import)

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


