---
layout: post
title: "How Oceanbase synchronize states between updateServers "
description: ""
category: 
tags: [archtecture, oceanbase]
---

Reference
https://github.com/alibaba/OceanBase-0.5/blob/master/doc/OceanBase%200.5%20SQL%20%E5%8F%82%E8%80%83%E6%8C%87%E5%8D%97.pdf

See chapter 3. 

Each Availabilty Zone (AZ) has only one RootServer (RS) and UpdateServer(US). RS and US are normally co-located on the same machine. Replication is by deploying new RSs and USs in other AZs. OB uses quorum write so AZ number is normally 3 or 5. 

Since active US handles all write requests, it is responsible for populating CommitLog (CL) and sync it to backup USs. To ensure strong consistency, OB uses Paxos to sync CLs between USs.  

Also note that CL is a write ahead log. This means the active US will finish syncing CL to backup USs before changing values in memory.

Sequence of actions
---------
1. The active US writes new CL entries to a Paxos state machine, asynchrounsly    

Now on each of the backup USs
```
	a. gets updates changes in CL and store it in a circular buffer. If buffer is full then it returns error to the active US

	b. acks an nsync, and mark itself as in progress to the active US. This ack works as heartbeat to extend lease.

	c. changes its status to sync, and synchronously write CL in buffer onto the journal file on disk
	
	d. acks journalled transaction back to the active US

	e. submit change atomically
```

2. The active US synchronously writes changes in CL into journal file on disk. Note that it is after the sync CL step so that journalling can happen concurrently on all USs.

3. Once the active US knows a majority of USs have journalled successfully. It submits change atomically and answer the client that commit as successful 


Picking the active US
----------
To pick the active US, OB gets at least two USs reporting the max timestamp of their CLs, and pick the one with highest timestamp as the active US. OB uses this scheme to defend against the following cases

1. Old active US is down/network partitioned. In this case, because OB's availablity is quorum-based, we know there is at least 2 USs alive that can join election. Otherwise, the whole system is unavailable anyway. 

2. zombie active US (server A) with lot of unsynced change in CL rejoined and participated in a fresh leader election. 

```
a. If A got elected, its local CL may diverge with CL in Paxos.
Consider the case where US B becomes the active US while A is down. B may succesfully commit changes, which is not available in A's CL, but exists in CL in Paxos.

b. Reconcile such conflict is slow and error-prone. 

c. With OB's scheme, B will just be re-elected the active   

d. This scheme works on condition that clocks on each server is not two far away, e.g., A's clock is not too far ahead of B's. Since leader election happens only after master lease expired, we know when A tries to join the election, at lease one full master lease period(by default 10s) has elapsed. Syncing server times to within 10s diff is easy task   
```
