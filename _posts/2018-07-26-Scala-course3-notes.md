---
layout: post
title: Scala-course3-notes
date: 2018-07-26
---
week 1: parallel computing
---
<details><div markdown="1">
- def:  
  a type of computation in which many calculations are performed at the same time
- vs. concurrent programming:
  - parallel:  
    uses parallel hardware to execute computation more quickly. => Efficiency
  - concurrent:  
    may or may not execute multiple executions at the same time. => modularity, responsiveness
- granularity levels:
  - bit-level
  - instruction-level
  - task-level
- parallel hardware:
  - multi-core processors
  - symmetric multiprocessors
  - GPU
  - FPGA
  - computer clusters
- threads:  
  each process can contain multiple independent concurrency units called threads (sharing the same memory address space)
  ```scala
  class HelloThread extends Thread {
    override def run() {
      println("hello world!")
    }
  }
  val t = new HelloThread
  t.start()
  t.join()
  ```
  when t.join(), the main thread blocks until HelloThread finishes.
- atomicity
  - the synchronized block
    ```scala
    private val x = new AnyRef{}
    private var uidCount = 0L
    def getUniqueId(): Long = x.synchronized {
      uidCound = uidCount + 1
      uidCount
    }
    ```
  - composition with the synchronized block
    ```scala
    class Account(private var amount: Int = 0) {
      def transfer(target: Account, n: Int) =
        this.synchronized {
          target.synchronized {
            this.amount -= n
            target.amount += n
          }
        }
    }
    ```
- deadlock
  - resolving deadlocks:  
    always acquire resources in the same order
- memory model:  
  a set of rules that describes how threads interact when accessing shared memory
  - two threads writing to separate locations in memory do not need synchronization
  - a thread X that calls ```join``` on another thread Y is guaranteed to observe all the writes by thread Y after ```join``` returns
- running computations in parallel:
  ```scala
  parallel(e1, e2)
  ```
  - Monte Carlo method to estimate Pi
    ```scala
    def monteCarloPiPar(iter: Int): Double = {
      val ((pi1, pi2), (pi3, pi4) = parallel(
        parallel(mcCount(iter/4), mcCount(iter/4)),
        parallel(mcCount(iter/4), mcCount(iter - 3 * (iter / 4)))
      ))
      4.0 * (pi1 + pi2 + pi3 + pi4) / iter
    }
    ```
- first-class tasks
  ```scala
  val t1 = task(e1)
  val t2 = task(e2)
  val v1 = t1.join
  val v2 = t2.join
  ```
</div></details>

