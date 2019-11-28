---
layout: post
title: "Hashmap and ConcurrentHashmap"
description: ""
category: 
tags: [java, interview]
---

## Hashmap

* Array init size defaults to 16. Double the size when it is 75% full
* Length is always a power of two, because `hash%length==hash&(length-1)`
  * How to avoid/optimize rehash when resizing
* When array length > 64 and some link list(LL) length > 8, this LL will be changed to red-black tree(RBT)
* When iterating entrys, prefer EntrySet to retrive k & v at the same time.

### Hashmap's concurrency problem

* When insert two elements with the same hash, while there is no existing node at the entry, in `putVal()`
```java
    if ((p = tab[i = (n - 1) & hash]) == null) 
            tab[i] = newNode(hash, key, value, null);

```
and one node will be overwritten
* When `resize()`, this may introduce a cycle in the link

## ConcurrentHashMap

* How multi-threads increase sizes in parallel?
* How to ensure we can still operate when resizing?

### Node type

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        volatile V val;
        volatile Node<K,V> next;

//Note that Node.setValue gives UnsupportedOperationException(),i.e., Node itself is immutable
//....
```

### Put

* Calculate hash code and locate the node (init if necessary)
  * If node is empty, CAS + self spin to write
  * if hashcode == MOVED == -1, this means resizing is underway
  * Otherwise, write the data with synchronized. Note it no longer uses `ReentrantLock`
  * Similar to hashmap, convert to RBT if meets the threshold 


### Concurrency control


* Array of Node. `put()` is done by CAS first, and then synchronize on the node if CAS failed
* `get()` does not synchronize on objects at all - it is protected by the volatile

