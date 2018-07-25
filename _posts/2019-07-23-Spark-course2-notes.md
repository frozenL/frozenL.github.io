---
layout: post
title: Spark-course2-notes
date: 2018-07-23
---
week 1
---
<details><div markdown="1">
- pattern matching
  - e.g. 
    ```scala
    val f: String => String = { case "ping" => "pong"}
    f("ping")
    ```
  - partial functions
    - e.g.
    ```scala
    val f: PartialFunction[String, String] = { case "ping" => "pong"}
    f.isDefinedAt("ping")
    f.isDefinedAt("pong")
    f("ping")
    ```
- for
  - queries with for
    - remove duplicate: distinct
    ```scala
    { for {
        b1 <- books
        b2 <- books
        if b1.title < b2.title
        a1 <- b1.authors
        a2 <- b2.authors
        if a1 == a2
      } yield a1
    }.distinct
    ```
  - translation of for:  
    the Scala compiler expresses for-expressions in terms of map, flatMap and a very lazy variant of filter
    - ```for (x <- e1) yield e2``` is translated to ```e1.map(x => e2)```
    - ```for(x <- e1 if f; s) yield e2``` is translated to ```for(x <- e1.withFilter(x => f); s) yield e2```
    - ```for(x <- e1; y <- e2; s) yield e3``` is translated to ```e1.flatMap(x => for(y - e2; s) yield e3)```
- <span style='color: red'>Monads</span>
  - e.g.
  ```scala
  trait M[T] {
    def flatMap[U](f: T => M[U]): M[U]
  }
  def unit[T](x: T): M[T]
  ```
  - examples
    - List: unit(x) = List(x)
    - Set: unit(x) = Set(x)
    - Option: unit(x) = Some(x)
    - Generator: unit(x) = single(x)
  - map  
  ```scala
    m map f == m flatMap (x => unit(f(x)))
            == m flatMap (f andThen unit)
  ```
  - Monad Laws: to qualify as a monad
    - associativity:
    ```scala
    m flatMap f flatMap g == m flatMap(x => f(x) flatMap g)
    ```
    - left unit:
    ```scala
    unit(x) flatMap f == f(x)
    ```
    - right unit:
    ```scala
    m flatMap unit == m
    ```
</div></details>

week 2: lazy evaluation
---
<details><div markdown="1">
- structural induction on Trees:  
  to prove a property P(t) for all trees t of a certain type
  - P(l) holds for all leaves l of a tree
  - for each type of internal node t with subtrees s1, ..., sn, show that P(s1) ^ ... ^ P(sn) implies P(t)
- streams
  - delayed evaluation:  
    avoid computing the tail of a sequence until it is needed for the evaluation result
  - definition
    ```scala
    val xs = Stream.cons(1, Stream.cons(2, Stream.empty))
    Stream(1,2,3)
    (1 to 1000).toStream
    ```
  - #:: operator:
  ```x #:: xs == Stream.cons(x, xs)```
- lazy evaluation
- computing with infinite sequences:
  - all natural numbers:
    ```scala
    def from(n: Int): Stream[Int] = n #:: from(n + 1)
    val nats = from(0)
    nats map (_ * 4)
    ```
  - the sieve of eratothenes:  
    an ancient technique to calculate prime numbers
    ```scala
    def sieve(s: Stream[Int]): Stream[Int] = 
      s.head #:: sieve(s.tail filter (_ % s.head == 0))
    ```
</div></details>

week 3: functions and state
---
<details><div markdown="1">
- stateful objects:
  - definition: var in place of val
  ```scala
  var x: String = "abc"
  var count = 111
  ```
- identity and change
  - once we allow the assignment, then x and y are different
  ```scala
  val x = new BankAccount
  val y = new BankAccount
  ```
  - operational equivalence:  
  two definitions x and y are operationally equivalent if no possible test can distinguish between them.
  - the substitution model ceases to be valid when we add the assignment: e.g.
  ```scala
  val x = new BankAccount
  val y = x
  ```
  is equivalent to
  ```scala
  val x = new BankAccount
  val y = new BankAccount
  ```
- loops:
  - while
  - for-loops: not for-expressions  
    - e.g.
    ```scala
    for(i <- 1 until 3) { System.out.print(i + " ")}
    ```
    - translates to foreach
    ```scala
    def foreach(f : T => Unit): Unit = 
    ```
</div></details>


  