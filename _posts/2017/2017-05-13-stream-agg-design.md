---
layout: post
title: "Design Discussion: Streaming Analystics Example "
description: ""
category: 
tags: [Architecture]
---
Scenario
-----------
Suppose we run an e-commerce web site, we want to calculate the most sold 10 items in the last 24 hours. Ideally, we want to see the 


Functional Design
----------
For each purchase, we need to generate a (itemId, quantity, metadata) message and add it to our queue.


Q: how many shards/processors we need for the queue?
--------------
Assuming we have 10k purchase requests per second, we give each processor 1k per second load to handle the peak load => so we need 10 shards, we can easy have a margin of 5x. However, we have to do load testing on a POC cluster to verify our napkin calculation is sound


Q: Are we going to pre-compute the result or compute it on the spot?
--------------
A: given that read traffic is easily 10x higher, we should try pre-computing, but at the stage, let's try computing on the spot first, and then optimize it.

Q: Since we run windowed aggregation, what is the sliding window?
------------
Assume we slide once every hour, then in db we need to store (itemId, day-hour) -> (sum in that hour) k-v pair

So whenever a message arrives, we can do the following
```
1. update the current (itemId, day-hour) kv pair

2. do a range scan (ItemId, current-day-hour -24) to (ItemId, current-day-hour), and compute the new sum. Storage engines like c* and rocks support such ordered scans efficiently.

3. Compute the new sum in the last 24 hours, and then try updating the existing top10 list => here we see we probably want to pre-compute 

```
Q: how do we update the existing top 10 list?
------------
Since some data maybe stale, we will have to calculate all 11 (10 existing + 1 new comer) to get up-to-date info, now we can see that we should pre-compute and populate the store for serving


Q: what if we have no message coming, and our data is stale? 
----------
Idea 1: then we need to insert the dummy data from time to time to keep it fresh, o
Idea 2: then upon read, we need to add metadata on when the top 10 is last calculated, if it is calculated more than 1 hour ago, we know the data is stale, the reader will trigger a complete calc


Deployment
------------
Consider a multi-DC scenario, the request may go into different dcs, therefore, we need multiple aggregate clusters acorss each DCs 

Consider an active-passive scenario, on active failover, the passive will take over, and when active comes back, the passive will reverse sync to the active

The underlying data store, should follow the similar active-passive model, i.e., one write master, and all others are read-slaves

Note that its hard to do without the aggregation cluster, because we may have same itme sold at two different DC at the same time
