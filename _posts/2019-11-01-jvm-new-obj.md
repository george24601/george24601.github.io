---
layout: post
title: "What happens when JVM creates a new object"
description: ""
category: 
tags: [interview, java]
---

### Sequence of actions

* Every time we call `new`, a new object is created. Otherwise, autoboxing may use constant values in the constant pool
* Load the class via class loading if not done yet 
* Allocate the memory
  * When the heap memory segmented, often a result of CMS, maintain a list on which segments are available.
  * When the heap memory is not segmented, i.e., with Serial and ParNew GC, put used on one end, unused on the other, use a pointer as the separator
  * To preserve thread safety of memory allocation
    * TLAB: reserve a segment for each thread in Eden
    * When TLAB is exhausted, use CAS+retry
  * May maintain a separate handle pool, and the stack just points to the handle, so that after GC, no need to change the reference on the stack. The tradeoff is that any access to the data needs to go through 2 pointers
* Init 0 value
* Set object header
* Execute init(). 
  * Parent clas first. Instance code block first and then constructor
