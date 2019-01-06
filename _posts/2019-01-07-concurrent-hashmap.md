---
layout: post
title: "On Concurrent Hashmap"
description: ""
category: 
tags: [java, interview]
---

### Size
* Array init size defaults to 16. Double the size when it is 75% full
* Length is always a power of two, because `hash%length==hash&(length-1)`
* When array length > 64 and some link list(LL) length > 8, this LL will be changed to red-black tree(RBT)

### Other related collection types

* Vector: Synchronized list
* HashTable: rarely used
* TreeSet: RBT-based set
* LinkedHashSet: implemented by LinkedHashMap
* LinkedHashMap: inherits Hashmap, but add a bidirectional linked list, so to keep k-v's insertion order

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

### Concurrency control

* Array of Node. Put() is done by CAS first, and then synchronize on the node if CAS failed
* Get() does not synchronize on objects at all - it is protected by the volatile
