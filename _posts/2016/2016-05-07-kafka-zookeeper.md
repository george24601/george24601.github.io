---
layout: post
title: "Role of Zookeeper in Kafka"
description: ""
category: 
tags: [classic]
---

Map from partition to replica list
-------
```
  1. As a comparison, in GFS, it is the chunker server that keeps track of truck info. The master known which chuck is on which server
through heartbeats

  2. As common in most master/replication problems, this metadata has a version/epoch to handle the stale master/fail-recover problem

```

For each partition, which replica is the leader and which replicas are in sync
--------
```
  1. leader epoch is to handle stale leader

  2. version effctively handles membership view version

  3. What the role of controller? Similar to the master server in GFS

```

Broker information
-------
```
  This is the membership managment storage, along with view number, in active-passive replication
```

Controller id and epoch
---------
```
  1. Compare this with the master lease technique
  
  2. Controller will force alive memebers to elect a new primary/leader, i.e., like the master in GFS
```
