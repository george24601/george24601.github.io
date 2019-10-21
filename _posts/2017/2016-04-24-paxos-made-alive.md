---
layout: post
title: "Paxos Made Alive"
description: "My Notes and Study Questions"
category: 
tags: [paper]
---

"Paxos Made Simple" mentions that a common optimization is to have a distinguished proposer. This single-master raises a few problems

1. How can a replica become a coordinator?
2. If coordinator instance does not change between paxos instances. There is an optimization we can use, and why does it work?
3. Why we can not serve read request directly out of master copy/db?
4. In response to 3, Why Chubby introduces master leases? How would read request served without master leases?
5. How is master lease renewed? Why the lease timeout in the master is shorter than that in the replicas?
6. During a network partition, a new master is elected, but the old master may still exist in the other partition. When network
reconciles, what is the bad case that may happen between the 2 masters? How does Chubby defend against this case?
7. How do they detect master turnover? What is its semantics?

To help reduce the log size, we need to take snapshots, which introduces a few classic problems related to log + snapshot

1. Why it is application that takes snapshot?
2. What is in the snapshot handle? why is it there?
3. Steps of taking a snapshot


### Other problems

1. One of the premises of "Paxos Made Simple" is the non-Byzantine failure. However, disk may be emptied, or the file may be corrupted. How does Chubby mitigate these 2 threats?
2. Paxos asks for replicas generate ever-increasing number from disjoint sets. Given some example schemes
3. What kind of system Paxos is in terms of CAP?
4. What is the purpose of safety mode and liveness mode testing?
