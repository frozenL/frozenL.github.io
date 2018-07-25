---
layout: post
title: Scala-course1-notes
date: 2018-07-11
---

setup Eclipse
---
* [import into Eclipse](https://stackoverflow.com/questions/38048451/unable-to-work-on-imported-sbt-project-on-eclipse-scala-ide)

week 1
--- 
<details><div markdown="1">
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
</div>
</details>

week 2: Higher order functions
---
<details><div markdown="1">
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
</div></details>

week 3: data and abstraction
---
<details><div markdown="1">
- abstract classes:
```scala
abstract class IntSet {
  def incl(x: Int): IntSet
  def contains(x: Int): Boolean
}
```
- implementation:
```scala
class Empty extends IntSet {
  def incl(x: Int): IntSet = false
  def contains(x: Int): Boolean = new NonEmpty(x, new Empty, new Empty)
}
```
- overriding:
```scala
class Sub extends Base {
  override def foo = 2
}
```
- object definition: defines a singleton object, no other instances can be created
```scala
object Empty extends IntSet {
  ...
}
```
- <span style="color:red">dynamic binding</span>:  
the code invoked by a method call depends on the runtime type of the object that contains the method
- class organisations
  - pakages
  ```scala
  package progfun.examples
  object Hello {...}
  scala progfun.examples.Hello
  ```
    - named imports:
      - ```import week3.Rational```
      - ```import week3.{Rational, Hello}```
    - wildcard imports: ```import wee3._```
  - traits: like an abstract class, resemble interfaces in Java  
  classes can inherit from at most one class but arbitrary many traits
  ```scala
  trait Planar {
    def height: Int
    def width: Int
    def surface = height * width
  }
  class Square extends Shape with Planar with Movable...
  ```
  - exceptions:
    - e.g. ```throw Exc```
    - type: ```Nothing```
  - null type: ```val x = null```
- polymorphism
  - meaning:
    - the function can be applied to arguments of many types
    - the type can have instances of many types
  - <span style='color:red'>principle forms</span>:
    - subtyping:
    - generics:
  - cons-list:
    - Nil
    - Cons
    - in Scala, polymorphic:
    ```scala
    trait List[T] ...
    class Cons(val head: T, val tail: List[T]) extends List[T]...
    class Nil[T] extends List[T] ...
    ```
  - Type parameter
    - for classes
    - for functions:
    ```scala
    def singleton[T](elem: T) = new Cons[T](elem, new Nil[T])
    singleton[Int](1)
    ```
  - Type inference:  
    The Scala compiler can deduce the type parameters from the value arguments of a function call
  - <span style = 'color:red'>Type erasure</span>:  
    assume that all type parameters and type arguments are removed before evaluating the program
</div></details>

week 4: types and pattern matching
---
<details><div markdown="1">
- pure object orientation:  
every value is an object
- functions as objects:
  function values are treated as objects in Scala  
  A => B is an abbreviation for the class scala.Function1[A, B]  
  Function1: the function for a single argument
  ```scala
  package scala
  trait Function1[A, B] {
    def apply(x: A): B
  }
  // traits Function2, Function3, ...
  ```
  - expansion of function values:
    - anonymous function:
    ```scala
    (x: Int) => x * x
    new Function1[Int, Int] {
      def apply(x: Int) = x * x
    }
    ```
    - <span style = 'color: red'>eta-expansion</span>
- type bounds
  - e.g. 
  ```scala
  def assertAllPos[S <: IntSet](r: S): S = ...
  ```
  - upper bound:```[S <: IntSet]```  
  S can be instantiated only to types that conform to IntSet
  - lower bound: ```[S >: NonEmpty]```
  - mixed bound: ```[S >: NonEmpty <: IntSet]```
  - covariance:  
  ```NonEmpty <: IntSet``` then ```List[NonEmpty] <: List[IntSet]```  
  Arrays in Java are covariant, not covariant in Scala because they are mutable  
  However, Lists are covariant in Scala (immutable)
  - Liskov substitution principle  
  if A <: B, then everything one can do with a value of type B should also be able to do with a value of type A
  - variance:
    - definitions: assume A <: B, C is
      - covariant if C[A] <: C[B], in Scala: 
        ```class C[+A] {...}```
      - contravariant if C[A] >: C[B]
        ```class C[-A] {...}```
      - nonvariant if neither C[A] nor C[B] is a subtype of the other
        ```class C[A] {...}```
    - typing rules for functions:  
      - if A2 <: A1 and B1 <: B2 then ```A1 => B1 <: A2 => B2```  
      - e.g.
      ```scala
      type A = IntSet => NonEmpty
      type B = NonEmpty => IntSet
      A <: B
      ```
      - functions are contravariant in their argument types and covariant in their result types
    - <span style='color:red'>variance check</span>
- decomposition
  - type tests:
  ```scala
  def isInstanceOf[T]: Booelean
  x.isInstanceOf[T]
  ```
  - type casts:
  ```scala
  def asInstanceOf[T]: T
  x.asInstanceOf[T]
  ```
  - pattern matching
    - case classes
    ```scala
    trait Expr
    case class Number(n: Int) extends Expr
    case class Sum(e1: Expr, e2: Expr) extends Expr
    def eval(e: Expr): Int = e match {
      case Number(n) => n
      case Sum(e1, e2) => eval(e1) + eval(e2)
    }
    ```
- List
  - right associativity: ```val nums = 1 :: 2 :: 3 :: Nil```
  - operations:
    - head
    - tail
    - isEmpty
  - List patterns:
    - Nil
    - p::ps
    - List(p1, ..., pn)
</div></details>

week 5: Lists
---
<details><div markdown="1">
- methods
  - xs.length
  - xs.head, xs.tail
  - xs.last, xs.init
  - xs take n: a list consisting of the first n elements of xs, or xs itself if it is shorter than n
  - xs drop n: the rest of the collection after taking n elements
  - xs(n)
  - xs ++ ys or xs ::: ys
  - xs.reverse
  - xs update (n, x): contains x at index n
  - xs indexOf x
  - xs contains x
- pairs and tuples
- implicit parameters
  - parameterisation with Ordering: ```scala.math.Ordering[T]```
    ```scala
    def msort[T](xs: List[T])(ord: Ordering) = 
      def merge(xs: List[T], ys: List[T]): List[T] = ... if(ord.lt(x, y)) ...
      ...
      merge(msort(fst)(ord), msort(snd)(ord))
      ...
    ```
  - implicit parameters:  
    leave out the actual parameter in the call
    ```scala
    def msort[T](xs: List[T])(implicit ord: Ordering) = 
      def merge(xs: List[T], ys: List[T]): List[T] = ... if(ord.lt(x, y)) ...
      ...
      merge(msort(fst), msort(snd))
      ...
    ```
- higher-order list functions
  - map
    ```scala
    xs map (x => x * x)
    ```
  - filters
    - xs filterNot p
    - xs partition p = (xs filter p, xs filterNot p)
    - xs takeWhile p:  
      the longest prefix of list xs that consisting of elements that all satisfy the predicate p
    - xs dropWhile p:  
      the remainder of the list xs after any leading elements satisfying p have been removed
    - xs span p: (xs takeWhile p, xs dropWhile p)
  - reduction of lists
    - sum: ```sum(List(x1, ..., xn))```
    - product: ```product(List(x1, ..., xn))```
    - reductLeft:
    ```scala
    List(x1, ..., xn) reduceLeft op = (...(x1 op x2) op ...) op xn
    def sum(xs: List[Int]) = (0 :: xs) reduceLeft (_ + _)
    def product(xs: List[Int]) = (1 :: xs) reduceLeft (_ * _)
    ```
    - foldLeft:  
    with an accumulator
    ```scala
    (List(x1, ..., xn) foldLeft z)(op) = (...(z op x1) op ...) op xn
    def sum(xs: List[Int]) = (xs foldLeft 0)(_ + _)
    def product(xs: List[Int]) = (xs foldLeft 1)(_ * _)
    ```
    - foldRight:
    ```scala
    (List(x1, ..., xn) foldRight acc)(op) = x1 op (...(xn op acc))
    ```
- structural induction
  - <span style='color:red'>referential transparancy</span>
  - prove a property P(xs) for all lists xs:
    - P(Nil) holds
    - if P(xs) holds then P(x::xs) holds
</div></details>

week 6: collections
---
<details><div markdown="1">
- Vector
  - create:
  ```scala
  val nums = Vector(1, 2, 3, -88)
  val people = Vector('Bob', 'James', 'Peter')
  ```
  - operations:
    - ```x +: xs``` create a new vector with leading element x, followed by all elements of xs
    - ```xs :+ x``` create a new vector with trailing element x, preceded by all elements of xs
- collection hierarchy
  - Seq: a common base class of List and Vector
    - Range: a sequence of evenly spaced integers
      - operations:
        - to (inclusive)
        - until (exclusive in the upper bound)
        - by (to determine step value)
      - e.g.
      ```scala
      val r: Range = 1 until 5 // 1, 2, 3, 4
      val s: Range = 1 to 5 // 1, 2, 3, 4, 5
      ```
    - operations:
      - xs exists p
      - xs forall p
      - xs zip ys
      - xs.unzip
      - xs.flatMap f:  
      apply a function that returns a sequence for each element in the list, and flattening the results into the original list
      - xs.sum
      - xs.product
      - xs.max
      - xs.min
  - Iterable: a common base class of Seq, Set, Map
- nested sequences
  - e.g. 
  ```scala
  (1 until n) flatMap (i => (1 until i) map (j => (i, j))) filter (pair => isPrime(pair._1 + pair._2))
  ```
  - for expression
    - form:```for(s) yield e```
    - e.g.
    ```scala
    for (p <- persons if p.page > 20) yield p.name
    for {
      i <- 1 until n
      j <- 1 until i
      if isPrime(i + j)
    } yield (i, j)
    ```
- Sets
- Maps: ```Map[Key, Value]```
  - e.g.
  ```scala
  val romanNumerals = Map('I' -> 1, 'V' -> 5, 'X' -> 10)
  ```
  - ```map get key``` returns:
    - None: if map does not contain the given key
    - Some(x)
  - ```withDefaultValue``` turns a map into a total function:
    ```scala
    val cap1 = capitalOfCountry withDefaultValue "<unknown>"
    cap1("Andorra") // "<unknown>"
    ```
- groupBy
  - e.g.
  ```scala
  fruit groupBy (_.head)
  ```
</div></details>
