---
layout: post
title: "TiDB vs CockroachDB"
description: ""
category: 
tags: [architecture]
---

Last updated: Apr 8, 2019

### Similar designs

* Both follow the architecture of a stateless SQL layer on top of a replicated, strongly consistent KV store. 
* Both use RocksDB to serve the KV store. Consistency is guaranteed by the standard write ahead log (WAL) + replicated state machine (RSM) model. WAL is replicated by raft.  
  * This architecture is inspired by Spanner/F1.
  * Each db has its own optimization on top of the vanilla raft. Will not dive deep into details here, because the actual optimization keeps evolving
* To implement transaction, both use the standard 2PC + MVCC idea. 
  * Both, by default, follows serializable concurrency model, implemented by lease reads.
* Both use online schema change, based on the paper "Online, Asynchronous Schema Change in F1",i.e., multi-stage schema change


The similarities end at this high level designs. Huge difference in implementation details

### Performance

* [The test I did in 2017](https://groups.google.com/forum/#!topic/tidb-user/WHo8o_ykbUE). Note that I was completely new to tidb, if I run it again, the numbers would be much matter. 
  * Use snapshot read instead of consistency read
* For standard innodb mysql, our planning expectation is 5k qps at 10 ms avg latency on an r4.4xlarge.
