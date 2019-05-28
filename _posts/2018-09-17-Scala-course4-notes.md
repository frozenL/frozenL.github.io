---
layout: post
title: Scala-course4-notes
date: 2018-09-17
---
week 1:
---
Spark: for large dataset, easy to scale
- more expressive
- performant: in terms of developer productively - interactive
- good for data science: enable iteration??

Books:
- learning spark
- spark in action
- high performance spark
- advanced analytics with spark
- GitBook: jaceklaskowski/Mastering Apache Spark 2
- databricks

shared memory data parallelism: data partitioned in memory and operated upon in parallel
- split the data
- workers/threads independently operate on the data shards in parallel
- combine when done

distributed data parallelism: data partitioned between machines, network in between, operated upon in parallel
- split the data over several nodes
- nodes independently operate on the data shards in parallel
- combine when done

Distribution concerns:
- partial failure: whether can be finished
- latency: time

Hadoop/MapReduce: fault tolerance ==> possible to scale to 100s or 1000s of nodes. involves disk and network. a distributed data infrastructure
Spark: fault-tolerance, different strategy for handling latency, using ideas from functional programming. involve memory and network. a data-processing tool


RDD(resilient distributed datasets)

word count:
```scala
val rdd = spark.textFile("hdfs://...")
val count = rdd.flatMap(line => line.split(" "))
               .map(word => (word, 1))
               .reduceByKey(_ + _)
```
- transformations and actions
  - transformation: returns new RDDs as a result, lazy
  e.g. map()
  - action: computes a result based on an RDD, eager
  e.g. reduce()
  - common transformations:
    - map
    - flatMap
    - filter
    - distinct
    - union(other: RDD[T]): RDD[T]
    - intersection(other: RDD[T]): RDD[T]
    - subtract(other: RDD[T]): RDD[T]
    - cartesian[U] (other: RDD[U]): RDD[(T, U)]
  - common actions:
    - collect(): Array[T]
    - count(): Long
    - take(num: Int): Array[T]
    - reduce(op: (A, A) -> A): A
    - foreach(f: T => Unit): Unit
    - takeSample(withRepl: Boolean, num: Int): Array[T], with or without replacement
    - takeOrdered(num: Int)(implicit ord: Ordering[T]), return the first num elements
    - saveAsTextFile(path: String): Unit
    - saveAsSequenceFile(path: String): Unit

  - benefits of laziness: e.g.
  ```lastYearsLogs.filter(_.contains("ERROR")).take(10)```
  doesn't get executed until the `take` action is applied

week 2
---
- reduction operations:
walk through a collection and combine neighbouring elements of the collection together to produce a single combined result. `fold, reduce, aggregate`
- foldleft non-parallelisable: may incur type error
- `aggregate[B](z: => B)(seqop: (B, A) => B, combop: (B, B) => B): B`: parallelisable --> has combop 
- pair RDDs: distributed key-value pairs
`RDD[(String, Property)]`
allow you to act on each key in parallel or regroup data across the network.
  - creating a pair RDD, e.g.
  ```scala
  val rdd: RDD[WikipediaPage] = ...
  val pairRdd = rdd.map(page => (page.title, page.text))
  ```
  - transformations
    - `def groupByKey(): RDD[(K, Iterable[V])]`
    `groupedRdd.collect().foreach(println)`
    - `def reduceByKey(func: (V, V) => V): RDD[(K, V)]`
      more efficient than `groupByKey + reduce`
      `eventsRdd.reduceByKey(_+_)`
    - `def mapValues[U](f: V => U): RDD[(K, U)]`
      `eventsRdd.mapValues(b => (b, 1))` ==> `(org, budget) -> (org, (budget, 1))`
    - keys
    - join
    - leftOuterJoin/rightOuterJoin
  - action
    - `def countByKey(): Map[K, Long]`

Inner Joins
-  `def join[W](other: RDD[(K, W)]): RDD[(K, (V, W))]`

Outer Joins: return a new RDD containing combined pairs whose keys don't have to be present in both input RDDs
- `def leftOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (V, Option[W]))]`: second element optional
- `def rightOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (Option[V], W))]`: first element optional
