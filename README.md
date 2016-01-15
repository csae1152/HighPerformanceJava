# HighPerformanceJava
Programming techniques for high-performance Java

GARBAGE COLLECTION TUNING (HOTSPOT JVM)
=======================================

- Generational Garbage Collection
- Serial / Parallel Garbage Collection
- Concurrent Garbage Collection (CMS)
- Garbage First Collector (G1)
- GC Profiling
- Throughput Tuning 
- Pause Tuning 
- G1 Tuning

Don't fear Parallel Scavenge

Today we cover how Parallel GC works. Specifically this is the combination of running a Parallel Scavenge collector over Eden and the Parallel Mark and Sweep collector over the Tenured generation. You can get this option by passing in -XX:+UseParallelOldGC though its the default on certain machine types.

Eden and Survivor Spaces
========================

In the parallel scavenge collector eden and survivor spaces are collected using an approach known as Hemispheric GC. Objects are initially allocated in Eden, once Eden is close to full 1 a gc of the Eden space is triggered. This identifies live objects and copies them to the active Survivor Space2. It then treats the whole Eden space as a free, contiguous, block of memory which it can allocate into again.

In this case the allocation process ends up being like cutting a piece of cheddar. Each chunk gets sliced off contiguously and then the slice next to is the next to be 'eaten'. This has the upside that allocation merely requires pointer addition.

Most common java programming errors:
====================================

1. NullPointerException
2. Race conditions
3. Leaks

Java 8 and performance issues
============================================

1. Lambda expressions 

New introduced lambdas in Java 8 have a few performance advantages over anonymous inner classes which you had to use before Java 8 to "mimize" lamdas in Java.

Elasticsearch, Redis and CouchDB in the view of low latency

1. Where are elasticsearch indexes stored ?
2. Is Redis In-Memory by default ?
3. CouchDB indexes are stored/cached from disk or memory ?
4. Dedicated CPU's and complex elasticsearch queries ?


