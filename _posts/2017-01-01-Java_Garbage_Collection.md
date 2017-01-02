---
layout: default
title: Java Garbage Collection
category: tech
tags: Java GC
year: 2017
month: 01
day: 01
published: true
summary: Garbage collection in Java
---

JVM provides automatic memory management through a program called as Garbage Collector. This blog talks about GC in general
and also the various Garbage Collectors in Java.

### Garbage Collection Overview
The Garbage Collection typically involves:

1. Mark - Starts from the root node of your application and walks the object graph and marks objects that are reachable
2. Sweep - Delete objects that are unreachable
3. Compacting - Compact the memory by moving around the objects and making the allocation contiguous than fragmented

Ideal characteristics of GC:

* Maximize Throughput
* Minimize Latency
* Minimize Footprint

#### Why Generational GC?

Based on empirically observed properties of most applications, it has been seen that most objects die young.
So the GC exploits this property and divides the heap into two main regions - **Young generation** and the **Old/Tenured generation**.
Most new objects all go into this young generation and when the young generation is filled up, a minor collection occurs
which will free up space in the young generation.
After a number of minor GC cycles, objects that survive those GC cycles are moved to tenured generation.
Eventually, the tenured generation will fill up and must be collected, resulting in a major collection, in which the entire heap is collected.
Major collections usually last much longer than minor collections because a significantly larger number of objects are involved.

### Generational collectors cycle
Heap Space is divided into Two parts:

1. **Young Generation**: New objects are all placed here
   Further divided into

   i. **Eden Space** : New Object goes here first. e.g. new HashMap

   ii. **Survivor Space**: If Eden space is full, GC will trigger and clean unreachable objects and place reachable objects in this place.
       That way Eden is entirely empty now
      There are two Survivor spaces: From and To
      When minor GC runs, it clean up Eden and moves reachable objects to Survivor space.
      When minor GC runs again (when Eden is full), it moves reachable objects to next Survivor and also reachable objects from previous Survivor here.
      That way both Eden and one of the Survivor space is empty

2. **Old/Tenured generation**: Objects which survive several cycles of GC and still has references pointing to it.

### Minor and Major Cycles

2 cycles: Minor and Major - both are usually stop the world meaning your entire Java application is halted.

* **Minor** - goes through the young generation and cleans up Eden/Survivor space
* **Major** - goes through entire Heap and cleans up both Old and Young.

If objects in Survivor survive a specific threshold of GC cycles (-XX MaxTenuringThreshold), objects are moved to Old Generation and Survivor space is freed.


### GC options

Option | Description
------------ | -------------
-XX:+UseSerialGC  | Single threaded GC on old and young - OK for small apps
-XX:+UseParallelGC | Young GC uses parallel GC, Old Gen uses single thread GC
-XX:+UseParallelOldGC |Both Young and Old generation uses multi-threaded Parallel GC - Useful for Batch apps, better throughput
-XX:+UseConcMarkSweepGC | Enables concurrent GC for old GC.
-XX:+UseParNewGC  | Parallel GC for Young gen - Adding this option to CMS gives - parallel for Young gen and CMS for old gen
-XX:+UseG1GC | Enable G1 GC (default on JDK 1.7)

## References

- [Garbage collection in Java, with Animation and discussion of G1 GC ](https://www.youtube.com/watch?v=UnaNQgzw4zY)
- [Java (JVM) Memory Model ](http://www.journaldev.com/2856/java-jvm-memory-model-memory-management-in-java)
- [JStat](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html)
- [G1 GC](https://www.infoq.com/articles/G1-One-Garbage-Collector-To-Rule-Them-All)
- [G1 Garbage Collector Details and Tuning by Simone Bordet](https://www.youtube.com/watch?v=Gee7QfoY8ys)
