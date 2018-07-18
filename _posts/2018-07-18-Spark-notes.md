---
layout: post
title: Spark-notes
date: 2018-07-11
---

setup Eclipse
---
* [import into Eclipse](https://stackoverflow.com/questions/38048451/unable-to-work-on-imported-sbt-project-on-eclipse-scala-ide)

week 1
---
semicolons:
normally omitted, unless there are more than one statements on a line
- expressions that span several lines: 
  - write in parentheses
  - write the operator on the first line

nested functions: avoid name-space pollution

blocks and visibilities:

the definitions inside a block shadow definitions of the same names outside the block
- lexical scoping: eliminating redundant occurrences of the same parameters

tail recursion: if a function calls itself as its last action, the function's stack frame can be reused

specify in Spark, e.g.:
```
@tailrec
def gcd(a: Int, b: Int): Int = ...
```
