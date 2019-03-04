---
layout: post
title: "On Java Synchronized"
description: ""
category: 
tags: [interview, currency]
---

### Synchronized

* every object has a monitor lock, montiorenter and monitorexit
* Synchronized static methods are synchronized on the class object of the class the synchronized static method belongs to. 
* Every thread has an available monitor record list, and a global available list, every locked object is assocated with a montior, and monitor has an owner section to store the thread holding the the monitor
* Mark word in object header,e.g., hashcode, ago, bias, lock tag. Object header = mark word + type pointer
* Klass Pointer to the class's meta data
* JVM will take note of the thread id/address of Thread pointer  as the owner of the syncrhonzied object

### CAS

* Heavily used by atomic types in java.util.concurrent
* ABA problem: the value changes in the sequence of A-B-A, so from CAS pov the value is not changed since the last change on A, but it did in fact!
* By default PreBlockSpin = 10. Note that locks can only upgrade but not downgrade
* LongAdder in Java 8
  * If the current threads too much, it will split the addtions into a cell array, each with its own CAS
  * If one failed to CAS one cell, LongAdder will try a different cell.
  * The final result is the sum of base and all values from cells

### Biased lock

* Thread will store its id in the lock object's mark word. When entering and leaving sync block it will not use CAS to lock/unlock, it will CAS the thread id instead. When the thread id is different from the current one:
  * If biased mark is 0, means it is LW lock already, use LW's CAS to acquire lock
  * If biased mark is 1, just CAS to change thread id to current id. If this CAS failed, will ask the cancel the biased lock, and pause the thread holding the biased lock to check if the that thread is still active
* Thread will not release biased lock itself. When no bytecode is running, the thread holding biased lock checks if the lock object is locked or not, and will cancel biased lock to restore to no-lock(01) or lightweight lock (00), depending on the answer

### Lightweight lock

* Other thread will spin to try acquiring the lock but will not block
* if the sync obj lock is 01, jvm will setup a Lock Record in the stack frame to store the lock object's Mark Word, and then use CAS to change object's mark word to pointer to the Lock Record, and set Lock Record's owner to object's Mark Word, if good, then Mark Word's lock status is 00
* if update failed, jvm will check object's mark word is pointing to current thread's stack frame, if only 1 waiting, the thread will just spin, if the thread spins too many times, or one thread is hold, one spinning, and a third coming, LWL will upgrade to HWL
* When release, try CAS to replace Mark Word back into object header. if failed, means there is contention with the lock, release the lock, upgrade to HWL, and wake up blocked threads

### Heavyweight lock

* When upgrading to heavy weight lock, lock status changed to "10", Mark Word stores pointer to HWL, all waiting threads wil block
* The synched object will point to the created monitor object, i.e., the monitor inside the object, which in turns based on MutexLock from OS - blocking and waking up requiring OS, i.e., from user mode to kernel mode
