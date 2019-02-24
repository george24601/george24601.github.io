---
layout: post
title: "On ThreadPoolExecutor"
description: ""
category: 
tags: [java, interview]
---

* NOT recommend to create via Executors static factory, instead, use ThreadPoolExecutor
* Recommend to use newCacheThreadExecutor, defaults to ThreadPoolExecutor
* Real TP interface is ExecutorService / ScheduledExecutorService
* Note that in general try NOT to create new threads explicitly. Let TP provide one.

### Constructor params

* corePoolSize
* long keepAliveTime 
 * When # of threads > cores, idle threads will wait this long before got terminated
 * Defaults to 60s
* maximumPoolSzie
* workQueue
 * Capacity defaults to Integer.MAX_VALUE!
 * Insertion is done by BlockingQueue.offer(), which will not throw exception or block.
* threadFactory
* RejectedExecutionHandler handler - when out of thread range or queue capacity


### On execute()

1. if # of threads < corePoolSize, execute addWorker,
2. if TP is running, if so, can you insert into the task queue? after insertion, double check if TP is running, if so, create a new thread, otherwise, remove the inserted task
3. if failed to add non-core thread, just reject

### runWorker(Worker w):
1. if it is first time to exec OR can get task from the queue
2. after acquiring the task, preexecute
3. execute
4. postexecute

### addWorker(Runnable firstTask, boolean core)
1. if the TP status >= SHUTDOWN, do nothing, just return
2. self-spin to check if the one we created is core thread, if so and current number < size, break from the loop and start creating the new thread
3. break retry and continue retry???
4. acquire the main lock for TP, add thread to workers, start the new thread
5. workers is a hashset

```java
//graceful shutdown
pool.shutdown();
while(!pool.awaitTermination(1, TimeUnit.SECONDS)){

}
```

