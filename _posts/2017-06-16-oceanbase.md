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
This design is similar to BT's SSTable + memtable approach. This means they share similar benefits and problems. One key difference is how they merge delta into history. 

In BT, the tablet server freezes the current memtable, and compact the memtable with a few SSTable to create new consolidated SSTable. During the compaction, the tablet server still accepts the r/w from client  

In OB, the whole replication zone stops accepting r/w requests while keeps receiving new paxos votes and logs. Once the merge is done, r/w traffics from clients is redirected to the zone again

Paxos-backed transaction
----------
This design is similar to Spanner's approach, where redolog is persisted as paxos state machine. The major difference is the use of 2PC

In Spanner, 2PC is not needed when transaction is on a single Paxos group. If only 1 Paxos group is involved, obviously, Paxos itself gives a strongly consistent log, that can be used as standard WAL in 1PC.

In OB, I could not find any reference to 2PC so far. Seems that it uses 1PC backed by a strongly consistent log approach

Consistency
----------
Spanner and OB share the goal that data are strongly consistent between DCs. This comes naturally because their log and metadata are in paxos. BT is evetually consistent between DCs, a guarantee brough by GFS. 


