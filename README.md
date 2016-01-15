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

Parallel in the context of parallel scavenge means the collection is done by multiple threads running at the same time. This shouldn't be confused with incremental GC, where the collector runs at the same time as, or interleaved with, the program. Parallel collection improves overall GC throughput by better using modern multicore CPUs. The parallelism is achieved by giving each thread a set of the roots to mark and a segment of the table of objects.

There are two survivor spaces, but only one of them is active at any point in time. They are collected in the same way as eden. The idea is that objects get copied into the active survivor space when they are promoted from eden. Then when its time to evacuate the space they are copied into the inactive survivor space. Once the active survivor space is completely evacuated the inactive space becomes active, and the active space becomes inactive. This is achieve by flipping the pointer to the beginning of the survivor space and means that all the dead objects in the survivor space can be freed at the cost of assigning to a single pointer.

Young Gen design and time tradeoffs
Since this involves only copying live objects and pointer changes the time taken to collect eden and survivor spaces is proportional to the amount of live objects. This is quite important since due to the generational hypothesis we know that most objects die young, and there's consequently no GC cost to freeing the memory associated with them.

The design of the survivor spaces is motivated by the idea that collecting objects when they are young is cheaper than doing a collection of the tenured space. Having objects continue to be collected in a hemispheric fashion for a few GC runs is helpful to the overall throughput.

Finally the fact that eden is organised into a single contiguous space makes object allocation cheap. A C program might back onto the 'malloc' command in order to allocate a block of memory, which involves traversing a list of free spaces in memory trying to find something that's big enough. When you use an arena allocator and consecutively allocate all you need to do is check there is enough free space and then increment a pointer by the size of the object.

Parallel Mark and Sweep
=======================

Objects that have survived a certain number of collections make their way into the tenured space. The number of times that they need to survive is referred to as the 'tenuring threshold'. Tenured Collections work somewhat differently to Eden, using an algorithm called mark and sweep. Each object has a mark bit associated with it. The marks are initially all set to false and as the object is reached during the graph search they're set to true.

The graph search that identifies live objects is similar to the search described for young generation. The difference is that instead of copying live objects, it simply marks them. After this it can go through the object table and free any object that isn't live. This process is done in parallel by several threads, each search a region of the heap.

Unfortunately this process of deleting objects that aren't live leaves the tenured space looking like Swiss Cheese. You get some used memory where objects live, and gaps in between where objects used to live. This kind of fragmentation isn't helpful for application performance because it makes it impossible to allocate objects that are bigger than the size of the holes.

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


