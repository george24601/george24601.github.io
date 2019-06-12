---
layout: post
title: "Product engineer's guide to the tidb migration"
description: ""
category: 
tags: [mysql, tidb]
---

### Shared by MySql and Tidb
* 90% of chance you should not use plain SELECT (no for update) inside transaction in either MySQL or TiDB. Most likely you need SELECT FOR UPDATE (SFU)
* SFU is a common solution to defend against writer skew and lost update problem. 
* No nestd transactions or remote call inside the transaction
* Do not use foreign key in DB even if they are supported
* Concurrent insertions into an auto-increment ID column may have cases where ID values do not appear in the increasing order

### Unique to Tidb
* Default isolation level is Snapshot Isolation (SI), which is similar to MySQL’s Repeatable Read (RR). It is stronger than Read Commited (RC), but not exactly same as RR
* Auto increment ID can guarantee uniqueness but not continuity. HIGHLY recommend move on from auto increment ID to ID generator such as snowflake.
* Hot key range problem in the kv storage problem during batch writes. Note that for int/bigint type, the key in tikv is the primary key
* SFU is using optimistic lock. This means we need to catch the optimistic locks exceptions from mysql client explicitly.

### Unique to Mysql
* Phantom read is possible at Repeatable Read but not SI



