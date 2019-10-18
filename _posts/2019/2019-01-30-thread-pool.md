---
layout: post
title: "On ThreadPoolExecutor"
description: ""
category: 
tags: [java, interview]
---

* NOT recommend to create via Executors static factory, instead, use `ThreadPoolExecutor`. Recommend to use newCacheThreadExecutor, defaults to `ThreadPoolExecutor`
* Note that in general try NOT to create new threads explicitly. Let TP provide one.
* To handle exception in the worker
  * try-catch inside the runnable
  * `submit()` to execute, `Future.get()` to handle ex
  *  `TPE.afterExecute()`
  * pass in `Thread.UncaughtExceptionHandler`
* Constructor params
  * corePoolSize
  * long keepAliveTime 
    * When # of threads > cores, idle threads will wait this long before got terminated
    * Defaults to 60s
  * maximumPoolSzie, i.e., we can use it to set constant size TP.
  * workQueue
    * Capacity defaults to Integer.MAX_VALUE!
    * Insertion is done by BlockingQueue.offer(), which will not throw exception or block.
  * threadFactory
  * RejectedExecutionHandler handler - when out of thread range or queue capacity
* The state of TPE
  * RUNNING
  * SHUTDOWN: shutdown(), no new task will be accepted, but will finish executing the jobs in the task queue
  * STOP: shutdownNow(), no new task, not executing tasks in the q
  * TIDYING: all tasks completed, will execute terminated()
  * TERMINIATED: after termiated() is run

### Building pieces of the TPE

```java
class Worker extends AbstractQueuedSynchronizer 
implements Runnable
{
       Runnable firstTask;
       final Thread thread;
       volatile long completedTasks;
}
```

* `thread` is created in the worker's ctor, by the thread factory passed in
* Worker is a nested class of the TPE. In fact, TPE has a reference to `Worker` too

```java
class FutureTask{
        Callable callable;
        private volatile int state;
        private Object outcome; // non-volatile, protected by state reads/writes
        private volatile Thread runner;
}

Future<?> submit(Runnable task) {
        RunnableFuture<Void> tftask = new Futuretask<T>(runnable,  null); //uses Executors.callable(runanble, null)
        execute(tfask);
        return ftask;
}
```

* `runner` is CASed as the `Thread.currentThread()` from null. This also acts as the mutex check 
* `callable` is turned from `runnable` by the Executor

### TPE.execute()

```java
void execute(Runnable command) {
        int c = ctl.get();

        if(wokerCountOf(c) < corePoolsize) {
                if(addWorker(command, true))
                        return;
                c = ctil.get();
        }

        if(isRunning (c) && workQueue.offer(command)){ //added to the blocking queue successfully
                int recheck = ctl.get();
                if(!isRunning (recheck) && remove(command))
                        reject(command);
                else if (wokerCountOf(recheck) == 0)
                        addWorker(null, false);
        } else if (!addWorker(command, false)){
                        reject(command);
        }
}
```

* `TPE.execute()` is the interface to outside, i.e., directly called by `submit()`
* If TPE is running, and we can add the command to the task q. Note that we have to double check the state again because the offer operation is not an atomic step of `isRunning` and try removing it it. Similarly, the two `else if` logics are quite similar because we essentialy performed the check twice.
* If `isRunning` try adding the worker. Note that `addWorker` side checks the TPE state, so when the TPE is not running, the new task will not be run 

### TPE.runWorker(Worker w):

```java
while (task != null || (task = getTask()) != null) {
    w.lock();
    if ((runStateAtLeast(ctl.get(), STOP) ||
         (Thread.interrupted() &&
          runStateAtLeast(ctl.get(), STOP))) &&
        !wt.isInterrupted())
        wt.interrupt();
    try {
        beforeExecute(wt, task);
        Throwable thrown = null;
        try {
            task.run();
        } catch (Throwable x) {
            thrown = x; throw new Error(x);
        } finally {
            afterExecute(task, thrown);
        }
    } finally {
        task = null;
        w.completedTasks++;
        w.unlock();
    }
}
```

* This is called from worker and, by extension, worker's thread since worker is an inner class
* Standard `lock()` and then `unlock()` inside `finally`
* `getTask()` checks if it can poll a task with `keepAliveTime`, if it couldn't work, it will return a null. This is how the non-core threads are terminated


### Shutting down TPE

```java
void shutdown() {
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try{
                checkShutdownAccess();
                advanceRunState(SHUTDOWN);//CAS in a loop to set state. No actual interruption
                interruptIdleWorkers();
                onShutdown();
        }finally{
                mainLock.unlock();
        }

        tryTerminate();

}

List<Runnable> shutdownNow() {
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try{
                checkShutdownAccess();
                advanceRunState(STOP);
                interruptWorkers(); //uses Thread.interrupt(), which just sets the mark, needs Thread.interrupted() inside that thread
                tasks = drainQueue();
        }finally{
                mainLock.unlock();
        }

        tryTerminate();
        return tasks;
}

threadPool.shutdown(); //have to wait for the termination explicitly
while(!threadPool.awaitTermination(60, TimeUnit.SECONDS)){
        //busy waiting here, most likely you need to add the max retry count
}
```

### FutureTask.run()

```java
if (state != NEW ||
    !UNSAFE.compareAndSwapObject(this, runnerOffset,
                                 null, Thread.currentThread()))
    return;
try {
    Callable<V> c = callable;
    if (c != null && state == NEW) {
        V result;
        boolean ran;
        try {
            result = c.call();
            ran = true;
        } catch (Throwable ex) {
            result = null;
            ran = false;
            setException(ex);
        }
        if (ran)
            set(result);
    }
} finally {
    // runner must be non-null until state is settled to
    // prevent concurrent calls to run()
    runner = null;
    // state must be re-read after nulling runner to prevent
    // leaked interrupts
    int s = state;
    if (s >= INTERRUPTING)
        handlePossibleCancellationInterrupt(s);
}
```

* CAS at the start uses current thread id as the optimistic lock. Hence, runner is set to null in the `finally`
