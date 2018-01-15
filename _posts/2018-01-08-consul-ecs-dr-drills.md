---
layout: post
title: "Disaster Recovery Drills for Consul on ECS" 
description: ""
category: 
tags: [middleware]
---

How to verify that consul is in a normal state
-------

1. the nodes request shows expected number of nodes

2. insert a value on the first node, check its value on the second node 


Drill 1: single node failure
----------
1. Stop an EC2 instance, verify autoscaling group kicks and spawns a new instance. 

2. Verify that the new instance joins the existing cluster and reaches a stable state

3. Verify that the new consul instance is at the correct version (to test the rolling upgrade case)

4. Restart the stopped instance at 1), force remove it after it joins the new stable cluster. Otherwise, force remove it. 


Drill 2: loss of quorum and complete outage
----------
1. Stop all remaining servers. Graceful leave may not be possible in this case

2. go to the -data-dir of each server, inside ratf/ dir add a raft/peers.json file. Find the server id in node-id file of data directory. Note peer.json should be same across all members, and servers not included will join rejoin later

3. Note that in the complete outage case, we need to restart ALL consul clients and registered services - they are not smart enough to do fail-over or retry even after our cluster is backup
