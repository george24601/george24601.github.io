---
layout: post
title: "TiDB vs CockroachDB: Compare and Contrast"
description: ""
category: 
tags: [architecture]
---

1. Both follow the architecture of a stateless SQL layer on top of a replicated, strongly consistent KV store. Both use RocksDB to serve the KV store. Consistency is guaranteed by the standard write ahead log (WAL) + replicated state machine (RSM) model. WAL is replicated by raft. 
  1. This architecture is inspired by Spanner/F1.
  2.Each db has different optimizations on top of vanilla raft. Will expand on this....

2. To map the SQL row to KV row
  1. In T, it is tableID/rowID → all column values
  2. In C, it is tableID/PK/columnID → column value for that column, i.e., C's approach is closer to a column

3. To implement transaction, both use the standard 2PC + MVCC idea.
  1. Will expand on the differences in their implementations later...
  2. Both, by default, follows serializable concurrency model, implemented by lease reads.

4. Equivalent of PD in Cockroach??

5. Both use online schema change, based on the paper "Online, Asynchronous Schema Change in F1",i.e., multi-stage schema change
