---
layout: post
title: "ReentrantLock and ReentrantReadWriteLock "
description: ""
category: 
tags: [interview, java]
---

### ReentrantLock vs synchronized

* Synchronized is based on JVM, RL is based on JDK/API
* RL supports `lockInterruptibly`, so that other waiting threads can stop waiting
* RL supports fair lock, synchronized is unfair lock
* `synchronized` uses `notify()/notifyAll()`, i.e, only one `Condition` for the whole monitor.
