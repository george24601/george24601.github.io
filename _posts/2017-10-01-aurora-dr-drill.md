---
layout: post
title: "DR Drills with AWS Aurora"
description: ""
category: 
tags: [database, architecture, aurora]
---

Objectives
-----
1. Verify that our services on Aurora can still perform within SLA with degraded services
2. Build tools and procedures for such drills, so that we can repeat drills against other services on different platform.


Note: all drills should happen in the context of our performance testing loading, i.e., new traffic coming.


Drill 1: Master db instance down. Node is still up
--------------
1. Read should be fine
2. Write failure should be gracfully handled, as per SLA
3. The application should recover after the failover is done, without modifying any endpoints 


Drill 2: Only the dispatcher between master and replica is down
-------------
1. This can simulate network partition
2. Service should be able to handle read/write inconsistency as per SLA
3. After failover is done, service should be able to handle the catch up

Drill 3: Read replica failure
----------
1. Setup: 10% of request blocks would fail, all read replicas would fail, amount of time to simulate failure = 1 min
2. Serivce should be able to perform as per SLA


Note: Aurora also has the capabity to perform disk failure and disk congestions, but drilling on such things brings debatable additonal value, until we gain more experience on that
