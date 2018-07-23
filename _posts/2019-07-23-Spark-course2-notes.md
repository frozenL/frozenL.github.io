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
