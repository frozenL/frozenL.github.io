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
```scala
@tailrec
def gcd(a: Int, b: Int): Int = ...
```

week 2: Higher order functions
---
- higher order functions: functions that take other functions as parameters or that return functions as results
- anonymous functions:
  - e.g. (x: Int) => x * x * x
  - e.g. sum(x => x, a, b), without defining a function with a name
- currying:
  - e.g. sum (cube) (1, 10)
  - e.g. def sum (f: Int => Int) (a: Int, b: Int): Int = ...
  - types: Int => Int => Int is equivalent to Int => (Int => Int)
- finding fixed points:
  - fixted point: x is a fixed point if f(x) = x
  - average damping: to estimate sqrt, instead of finding ```fixedPoint(y => y/x)(1.0)```,we use ```fixedPoint(y => (y + y/x) / 2)(1.0)```
- EBNF (Extended Backus-Naurform): grammar
  - Types
  - Expressions
- Definitions
  - function definition: e.g. ```def square(x: Int) = x * x```
  - value definition: e.g. ```val y = square(2)```
- parameters
  - CBV: e.g. ```(x: Int)```
  - CBN: e.g. ```(y: => Double)```
- classes
  - type: 
  ```scala
  class Rational(x: Int, y: Int) {
    def numer = x
    def denom = y
  }
  ```
  - constructors: 
    - primary constructor
    - alternatives:
    ```scala
    def this(x: Int) = this(x,1)
    ```
  - methods: packaged functions operating on a data abstraction in the data abstraction itself
  - object: 
  ```scala
  val x = new Rational(1,2)
  x.numer
  x.denom
  ```

  - requirement:
    - ```require(y != 0, "denominator must be zero")```
    - throws ```IllegalArgumentException```  
    - used to enforce a precondition on the caller of a function
  - assert:
    - ```assert(x >= 0)```
    - throws ```AssertionError```  
    - used to check the code of the function itself
- operators
  - infix notation:  
  any method with a parameter can be used like an infix operator
  - relaxed identifiers