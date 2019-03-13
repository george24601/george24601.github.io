---
layout: post
title: "Comparison of flushing mechanisms: InnoDB vs Kafka"
description: ""
category: 
tags: [mysql, interview]
---

### InnoDB
* Table and index data are cached in InnoDB buffer pool. It is recommend to keep innodb_buffer_pool_size 50 to 80 percent of system memory, by default it is only 128 MB.
* If buffer pool does not have the data, the data will be loaded from the page cache, which is in the system's address space (and from storage to page cache first if have not done so)
* `mmap` data files directly into its user process address space
* Uses fsync (`innodb_flush_method`) to flush the log to the disk immediately at commit (`innodb_flush_log_at_trx_commit`) or every 1 second (`innodb_flush_log_at_timeout`). Double buffering + sequential write means that this log flushing is fast. 


### Kafka
* By default, Kafka `fsync` asynchronously, i.e., the broker returns ACK after data is copied into page cache
* Also `mmap` its data file into user's process address space. Note that it is mapped to off-heap memory in JVM to prevent the  GC of actual log data object. (How to check JVM's native memory usage though?)
* During the send, Kafka copies directly from the page cache to the network card, and socket buffer copies a file descriptor but no data. Note all these data copying happens within the kernel space, i.e., no user -> kernel space copying happened.


Note: fsync just flush buffer specific to an open file
