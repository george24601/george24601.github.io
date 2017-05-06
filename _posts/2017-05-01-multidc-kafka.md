---
layout: post
title: "Notes on mutil-dc replication of kafka"
description: ""
category: 
tags: [kafka]
---
Based on https://www.slideshare.net/HadoopSummit/building-largescale-stream-infrastructures-across-multiple-data-centers-with-apache-kafka

Put replicas directly across DCs
-----------
```
1. on failed dc, the consumer will just switch to another replica.
2. when dc recovers, the existing catch up mechanism will kick in
3. If replicas are across region, latency will be high, plus demands a lot of cross dc bandwidth from replicator - hard for Kafka
```

Active-Passive
-------------
```
1. consumer on either active or passive dc
2. upon failure, the passive dc becomes the new active, consumer may need to switch too
3. note that offset may not match, because of at-least-once semantics of producer
4. normally for real time consumers, just start from the end and accept data loss
5. When the dc comes back, need to MM back changes from the new active to the former active - hard to manage the DC offset problem again.
```

Active-Active
-----------
```
1. each one has an active and an aggregate cluster
2. On failure and recovery, no need to reconfigure MM
3. to avoid aggregate cluster, we can preifx topics with DC tag,a nd confg MM to mirror remote topic only. and consumer need to sub to
   topics with both DC tags
```

How to make DB data available in all DCs
---------------
1. active-active: same consumer concurrently in both DCs
2. active-passive: only one consumer per dc at any given time

Ideally DB replication policy should be same as kafka cluster's
