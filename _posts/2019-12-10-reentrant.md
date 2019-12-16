---
layout: post
title: "On ReentrantLock"
description: ""
category: 
tags: [interview, java]
---

* ReentrantLock is just an encapsulation of AQS: it has a Sync object which extends AQS
* Need to implement `tryAcquire` in AQS

### vs synchronized

* Synchronized is based on JVM, RL is based on AQS in JDK
* RL supports `lockInterruptibly`, so that other waiting threads can stop waiting
* RL supports fair lock, synchronized is unfair lock
* `synchronized` uses `notify()/notifyAll()`, i.e, only one `Condition` for the whole monitor. No such limitation for `ReentrantLock`

State: 0 - nolock. Every acq +1, release -1

ReentrantReadWriteLock (RRWL) uses 16 bits to save write lock state, and 16 bits for read lock state
