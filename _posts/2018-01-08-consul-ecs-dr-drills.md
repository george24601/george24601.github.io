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

3. Go inside the container, verify that the new consul instance is at the correct version (to test the rolling upgrade case)

4. Restart the stopped instance at 1), force remove it after it joins the new stable cluster. Otherwise, force remove it. 


Drill 2: loss of quorum - multiple instances down
----------
1. Reboot all servers

2. The ECS should kick in and all servers restart and rejoin the same cluster successfully


Drill 3: loss of quorum - complete outage
-----------
1. Stop all but one server. ASG will kick in and spawn new instances, but the cluster is at a corrupted state, so any rejoin will fail.

3. Stop all newly spawned instanced expect the original alive one

??4. Change the -bootstrap-expect, note for the init bootstrap you need -boottrap-expect flag for sure
-need to take out ASG desired capacity

4. go to the -data-dir of each server, inside ratf/ dir add a raft/peers.json file. Find the server id in node-id file of data directory. This file should include ONLY the alive node

5. reboot the only alive server now. Make sure the log shows that peers.json is read correctly

7. Go into the new instances, and manually join the node to the cluster
