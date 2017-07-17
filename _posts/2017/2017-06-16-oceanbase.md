---
layout: post
title: "OB vs BT/Spanner/F1"
description: ""
category: 
tags: [archtecture]
---

Compare and contrast OceanBase(OB)'s design decisions with BT(BT)/Spanner/F1

Relational Model
----------
F1's relational model is closer to a hierarchical model. Physically rows from tables from the same hierarchy are stored together. No similar concept in OB  

Sharding
----------
Spanner, BT, OB all uses a chuck + location resolution-based approach, with a master server responsible for rebalancing chucks between servers. Therefore, manual sharding or re-sharding in traditional RDBMS does not exist in these dbs

Historial Data on SSD + delta in memory 
-----------
This design is similar to BT's SSTable + memtable approach. This means they share similar benefits and problems. 

BT and OB uses similar strategy when memetable becomes too big.

In BT, the tablet server freezes the current memtable and create a new memtable, and compact the memtable with a few SSTable to create new consolidated SSTable. During the compaction, the tablet server still accepts the r/w from client.  

OB uses similar strategy on each updateServer. But one key difference is WHEN OB triggers such compaction. Most commonly it uses has a rotating zone merge scheme. Similar scheme does not exist in BT 

Sequence of actions when OB is doing rotating zone merge

1. During off peak hours, e.g., 3AM, choose one zone that will compact memtable and SSTable to generate new SSTables 
2. Redirect all client requests from this zone to other zones. However, the selected zone still accepts Paxos events from other zones, to maintain the consistency of the commit log
3. Each updateServer in the selected zone apply the merge scheme similar to the one in BT. The new updates arrive during the merge goes to the new memtable.
4. After the whole merge is done, the zone starts accepting client request again.

During the zone merge, the overall throughput of the deployment will drop by 1/(total number of zones). To mitigate business impact, zone merges are always scheduled during off peak hours


Paxos-backed transaction
----------
This design is similar to Spanner's approach, where redolog is persisted as paxos state machine. The major difference is the use of 2PC

In Spanner, 2PC is not needed when transaction is on a single Paxos group. If only 1 Paxos group is involved, obviously, Paxos itself gives a strongly consistent log, that can be used as standard WAL in 1PC.

In OB, if a transaction involves only a single ObServer then it uses 1PC backed by a strongly consistent log approach. Otherwise, it uses 2PC for cross ObServer transactions. In general, need more information on how OB implements transaction

Consistency
----------
Spanner and OB share the goal that data are strongly consistent between DCs. This comes naturally because their log and metadata are in paxos. BT is evetually consistent between DCs, a guarantee brough by GFS. 

Join
--------
The F1 supports centered around hierarchical schemas, i.e., user should create table hierachies based on the join-query it aims to optimize for, similar to the doc design principles in Mongo.

In F1 join with table/source outside a table hierarchy is supported, using the repartition-hash join approach commonly seen in the data warehouse and Hadoop ecosystems. 

OB doesn't have the concept of hierarcical schemas, but similar to F1, DBA need to pre-define the join they want to optimize for.

But unlike F1, in OB join will automatically applies every time the table uses the FK is queries, i.e., if you have a table A that uses FK of another table B, then every time you query table A, corresponding FKed rows in B are queried and merged with rows in A.


Index
----------
Both F1 and OB has strong requirement of index support

Need more info on how OB implements index
