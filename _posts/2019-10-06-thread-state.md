---
layout: post
title: "On Java thread state"
description: ""
category: 
tags: [interview,java]
---

* Java threads:
  * New: by `new Thread()`
  * Runnable: after `Thread.start()`
  * Waiting: stay in the wait set, thread previosuly called:
    * Thread.join(), Need that thread to finish
    * `Object.wait()`. Need `Object.notify/notifyAll()` to become runnable.
    * LockSupport.park()
  * Timed_watiing: 
    * Thread.sleep(long)
    * Object.wait(timeout)
    * Thread.join(timeout)
    * LockSupport.parkUntil(long)
  * Blocked: need to acquire (monitor) lock but unable to. Stay in the entry set.
* OS threads:
  * new, ready, running, waiting, terminated
* Java thread has no running state - only runnable, it includes ready and running state in the OS thread's sense. Normally one thread executes about 10ms on CPU at a time. When at blocking IO, e.g., `socket.accept()`, the JAVA THREAD is still runnable instead of BLOCKED or WAITING, but at OS layer, the OS thread is WAITING.


