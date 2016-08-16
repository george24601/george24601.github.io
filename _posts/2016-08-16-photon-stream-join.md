---
layout: post
title: "Photon: Fault-tolerant and scalable joining of continuous data streams"
description: "Study questions"
category: 
tags: [streaming]
---

The requirement is near-exactly-once processing. That means they need to ensure at-most and at least processing

1. How is at most once implemented?

2. How is at least once implemented?

3. The joiner update the state and then flush the result, but these 2 operations are not in a single atomic step. How do they mitigate the
problem where state is updated, but joiner is unable to flush the result? Even after these mitigatations, what other scenarios could break the at least once semantics

4. How does event source know that which click is associated with with query?

5. Each click has a unique id, how/when/where is it generated?

6. The IdRegistry is shared across DCs with up to 100ms latency. in production each PaxosDB leader has ~ 10 requests per second. How do they
use so few requests to handle millions of clicks/write requests per min?

7. IdRegistry is, unsurprising, sharded, but they have the requirement to dynamically increase/decrease # of shards. How do they ensure that the same click, before and after sharding changes, will be routed to the same shard?

8. How do they mitigate outage at a single DC?

9. Consider at two different DCs processing the same click, how do they resolve the read-write conflict, specifically, the two DCs tries to
add the same click id to the registry at the same time?

10. How do they validate their join logic?

11. Due to business requirement and storage restrictions, they keep only the most N days of data. Due to the single master architeure of
paxos, it makes sense to run GC only at the master replica. But what if master changes and clock time drifted? How do they defend against it?


