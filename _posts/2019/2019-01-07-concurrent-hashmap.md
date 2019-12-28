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

### Members

```java
    /**
     * The array of bins. Lazily initialized upon first insertion.
     * Size is always a power of two. Accessed directly by iterators.
     */
    transient volatile Node<K,V>[] table;

    /**
     * The next table to use; non-null only while resizing.
     */
    private transient volatile Node<K,V>[] nextTable;
```

### Put

* Assuming the normal case, I deleted/commented/erased out some lines for the ease of reading
* CHM does not allow key or value to be null

```java
    final V putVal(K key, V value, boolean onlyIfAbsent) {
        int hash = spread(key.hashCode());
        int binCount = 0;//size in the list to decide if need to convert to RBT
        for (Node<K,V>[] tab = table;;) {
	Node<K,V> f;//current node at tbl 
	int n; //size of array, always a power of two 
	int i; //index at the tbl array
	int fh; //mod hash value
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
	    // (n-1) & hash, fast mod with 2^n, i,e. first time ever at this slot
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
//can't CAS, spin again
            }
            else if ((fh = f.hash) == MOVED)
	    //resizing is underway, let it finish first, may create a thread to speed up resizing
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {
		if (tabAt(tab, i) == f) { 
		//now that we are in mutex, first check if the node value remains valid
                        if (fh >= 0) {
                            binCount = 1;
			    //linked list upsert
                        }
                        else if (f instanceof TreeBin) {
                            binCount = 2;
			    //RBT upsert
                        }
                    }
                }
                if (binCount != 0) {
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
		    if (oldVal != null)
			//update case. no need to resize
                        return oldVal; 
                    break; //conclude spinning 
                }
            }
        }
	//attempt to help resize in case of insert
        addCount(1L, binCount); 
        return null;
    }
```

* `get()` does not use any mutex or CAS, why does it work?
  * Every time we update the table, we acquire the mutex first, i.e., in effect only 1 thread updating volatiles. Therefore, reading volatiles becomes thread safe 

### Concurrent resizing
Inside `transfer()`
* Mark copied node and empty node as `ForwardingNode`. When iterating, when we see such nodes, move to the next 
* Uses `MIN_TRANSFER_STRIDE` to lower bound the range
* No new thread is spawned. It reuses threads in `put()`.
