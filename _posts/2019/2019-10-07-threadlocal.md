---
layout: post
title: "On ThreadLocal"
description: ""
category: 
tags: [interview,java]
---

```java

static class ThreadLocalMap {

        private Entry[] table;
        int INITIAL_CAPCITY=16;
        static class Entry extends WeakReference.ThreadLocal<?> {
                Object value;
                Entry (ThreadLocal<?> k, Object v){ //v is what the code put in
                        super(k); //weak ref means entry k may become null
                        value = v;
                }
        }

        //on get and set, will expungeStaleEntry

}

public class Thread implements Runnable{

ThreadLocal.ThreadLocalMap threadLocals = null;

ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;

}

class ThreadLocal {

public void set(T value) {
	Thread t = Thread.currentThread();
	ThreadLocalMap map = t.threadLocals
	if(map != null)
		map.set(this, value);
        else
		createMap(t, value);
}

}

```

* standard techinque to replace `% pow(2, n)` with `&(pow(2,n)-1)`, and keep the size of the colleciton always `2^n`
* The table uses iteration to handle hash conflict, note that it will remove non-null entries whose key is null. This is part of the reason why the load factor is 2/3, lower than that of hashmap
* `ThreadLocal.ThreadLocalMap` is a member variable of `Thread`, so we can access it with Thread.currentThread();
* Weak reference to the `ThreadLocal` means that often we will have `Entry` with key = null. That is why during get() and set(), the code try to remove the stale entry. Otherwise, there is no way for outside code to break the strong reference to the `value` object by this time. This case is a common source of memory "leak" and is also the reason it is recommended to call `ThreadLocal.remove()` in a finally clause after processing is done.
