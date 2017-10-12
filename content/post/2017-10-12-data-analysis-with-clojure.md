---
title: Data munging with Clojure
author: ~
date: '2017-10-12'
slug: data-analysis-with-clojure
categories: []
tags:
  - clojure
---

# Introduction

In this blog post I want to talk about two important aspects of doing practical data analysis with Clojure,
namely "which data structure for my tabular data" and "which repl to use ?"

I have a background as data scientist, doing my analysis in R, but I am very interested in Clojure.

# Which data.frame ?

Most of the time as data scientist we spent in data munging. Being used to work in R,
data munging is to me equivalent of manipulating data in the form of a data frame.
(If data comes in other formats, we first convert it to a data frame before doing anything else).

This is the natural form of thinking as well in statistical terms, we have observations (rows) and variables (columns/features)

In R there are two contenders for this data frame structure. Base R has a "data.frame" and the tidyverse has a "data_frame", but for this discussion here, they are the same thing.

Python has its [pandas](http://pandas.pydata.org) and a whole data science ecosystem is based upon it.


What does Clojure have ?

Pure Clojure land as at least four ways to represent a data frame, and non is an accepted "standard" yet.


- incater's incanter.core.dataset for incanter 1.5.6 http://incanter.org/
- clojure.core.matrix.dataset (used as well by incanter > 1.9.0)  https://github.com/mikera/core.matrix
- collections of collections
- collections of maps


The first 2 are Clojure "records/types", so become java classes at runtime.
They store columns, basically.
The later two are pure Clojure structures.

The first 3 are column oriented, while the last is row-oriented.

The java class based structures need specific functions to be implemented and to operate on them,
which are existing in the core.matrix and incanter projects. They contain a broad range of operations on data frames.
So these have the concept of an API for data frames in Clojure. The same API could be implemented by different underlying data structures.
The data set can in fact use any core.matrix.matrix implementation as the underlying data structure.
Additionally each core.matrix.dataset is a core.matrix object.


The later two, do not need any specific functions, just standard Clojure functions.
So this approach does not have the concept of an API.
Certain operations on the Clojure data structures representing data frames, can be a bit cumbersome to use directly.
So there are some projects which add functions to wok on this "collection of maps":
https://github.com/sbelak/huri


I am not yet sure, which "style" of data structures I prefer.
Experimenting with both, made me believe that the "sequence of maps" data structure is better.
In the "huri" library it gets combined with Clojure.spec to add a bit of type safety.
This style integrates as well better with the new concept of transducers in Clojure.
One main use case of transducers are to compose data transformation pipelines.

Here is a link to a gorilla repl with some example huri code:
http://viewer.gorilla-repl.org/view.html?source=github&user=sbelak&repo=huri&path=examples/examples.cljw



# Which repl to use ?

The repl is fundamental as tool for exploratory data science.
I have tried 3 approaches:


- Clojure offers the standard repl, which is of limited use for explorative data science, as it lacks any way of plotting data.
It could work by plotting to files, or viewing them in an other tool.
- [Gorilla repl](http://gorilla-repl.org/) is a in browser repl allowing to combine mark down text and Clojure code.
- Emacs ORG mode with Clojure code blocks (http://orgmode.org/worg/org-contrib/babel/languages/ob-doc-clojure.html)


For a good repl experience, I think two additions to a repl are needed:

- [alembic](https://github.com/pallet/alembic) to hot-load new Clojure libraries inside a running repl
- better support for printing data sets 



# Completeness of Clojure's data munging ecosystem

The Clojure data munging ecosystem is still young and not complete.

Via Java interop, Clojure has of course access to a large number of specific libraries which cover a whole range of 
data science tasks.

For specific needs, interoperability with R might be very important.
There are several libraries available, like:

- [rojure](https://github.com/behrica/rojure) to call R from Clojure and transform data between them easily
- [gg4lj](https://github.com/JonyEpsilon/gg4clj) defines a Clojure DSL to describe an execute plots from R ggplot2 packages
- [opencpu-clj](https://github.com/behrica/opencpu-clj) for interop with R using [openCPU](https://www.opencpu.org/)

# Conclusion

The Clojure ecosystem offers already a lot for data munging, either directly or via Java/R interoperability.

I think the missing single, widely accepted way of representing a data frame in Clojure hinders progress for the moment.

There are two very different approaches on how to represent a data frame in memory, namely:

- API based (implemented by core.matrix.dataset + incanter)
- simple Clojure data structure based (no real implementation needed, just some convenience functions: [huri](https://github.com/sbelak/huri) )

Maybe the appearance of transducers will decide the winner.
Any "data transformation" implemented based on core.matrix.dataset will only work for a dataset.
Data transformation based on transducer will automatically work for simple Clojure data structures, but not necessarily for core.matrtix.dataset.

So eventually core.matrix.dataset could be extended to support transducers as well.
So "data transformations" could be implemented as transducers and work for both worlds.
