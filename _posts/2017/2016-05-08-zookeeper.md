---
layout: post
title: "ZooKeeper: wait-free coordination for internet scale systems"
description: "My notes and stufy questions"
category: 
tags: [paper]
---

### High level

1. What is the failure model assumed?
2. Critique the CAP of ZK system
3. What is the liveness and durability guarantee?
4. Why wait-free is not enough for coordination?



### Implementation details

1. Why use full path instead of handle to access znodes?
2. Why zk can process read locally at each replica? How about stale local copy problem?
3. Consier the case of new leader changing configs, we need to ensure that no partial config is read ever, even if new leader failed in the middle. Why the lock approach in Chubby can not handle this? How to construct a solution with zk to solve this?
4. ZK uses WAL and fuzzy snapshot to ensure exactly-once processing? Why the fuzzy snapshot works, even if it may not cache actual state at ANY givne time?
5. Why the transaction to be broadcasted need to be idempotent? Why in ARIES the operation inside the WAL does not need to be idempotent?
6. How does client use heatbeat to maintain connection with server?


### Implementaion patterns

1. Configuration
2. Group membership
3. Leader election
4. Leader information

### Role of Zookeeper in Kafka

1. Map from partition to replica list
  * As a comparison, in GFS, it is the chunker server that keeps track of truck info. The master known which chuck is on which server
through heartbeats
  * As common in most master/replication problems, this metadata has a version/epoch to handle the stale master/fail-recover problem
2. For each partition, which replica is the leader and which replicas are in sync
  1. leader epoch is to handle stale leader
  2. version effctively handles membership view version
  3. What the role of controller? Similar to the master server in GFS
3. Broker information. This is the membership managment storage, along with view number, in active-passive replication
4. Controller id and epoch
  1. Compare this with the master lease technique
  2. Controller will force alive memebers to elect a new primary/leader, i.e., like the master in GFS
