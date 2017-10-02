---
layout: post
title: "DR Drills with AWS Aurora"
description: ""
category: 
tags: [database, architecture, aurora]
---

Objectives
-----
1. Verify that our services on Aurora can still perform within SLA with degraded aurora service
2. Build tools and procedures for such drills, so that we can repeat drills against other services on different platform.

Note
--------
1. All drills should happen in the context of our performance testing load, i.e., new traffic coming.
2. Upon injected failure, aurora will just restart on the spot instead of failover, this means the service will wait until the master recovers
3. Between AZs, the replica lag is around 20ms. However, this just means the lags in cache, because aurora uses a share-disk design, the data is always consistent.
4. Failover times are typically 60-120 seconds.This means most connections will timeout during failover
5. Aurora also has the capabity to perform disk failure and disk congestions, but drilling on such things brings debatable additonal value, until we gain more experience on that

Crash dispatcher on writer
---------
1. master: create db instance
2. read replica: Read replica has been disconnected from master. Restarting Mysql => create db instance

Crash instance on writer
--------
1. master: DB instance restarted
2. read replica: DB instance restarted

Crash node on writer
--------
1. master: DB instance restarted
2. read replica: Read replica has fallen behind the master too much. Restarting Mysql => DB instance restarted

Failover
--------
1. old master:
```
a. Started cross AZ failover to DB instance
b. A new writer was promoted. Restarting database as a reader.
c. DB instance restarted
d. Completed failover to DB instance
```

2. new master:
```
a. Started cross AZ failover to DB instance
b. DB instance shutdown
c. DB instance restarted	
d. Completed failover to DB instance
```

Schedule
-----------
1. Such drill should happen once a month, before major version release.
2. The drill should start during low-traffic times, e.g., 2am local time

Drill 1: Failover
--------
1. Ensure traffic is going through our service. Either through traffic replication or load testing tool
2. Failover the current writer to a reader in a different AZ
3. During the failover, the service health check should be remain OK all the time
4. During the failover, write failure is expected, but read failure should not happen

Drill 2: Deleting a read replica
-----------
1. Ensure traffic is going through our service. Either through traffic replication or load testing tool
2. Ensure we have at least 2 healthy aurora instances running
3. Pick a read replica and delete it
4. During the failover, the service health check should be remain OK all the time
5. During the failover, write/read failure should not happen
6. Create a new read replica off the current writer


Drill 3: Deleting the current writer
--------
1. Ensure traffic is going through our service. Either through traffic replication or load testing tool
2. Ensure we have at least 2 healthy aurora instances running
3. Pick the current writer and delete it
4. During the failover, the service health check should be remain OK all the time
5. During the failover, write failure is expected, but read failure should not happen
6. Create a new read replica off the current writer
