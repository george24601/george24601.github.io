---
layout: post
title: "On Little's Law"
description: ""
category: 
tags: [interview]
---

```
Num of items in the system = rate of items arriving in the system * time each item stay in the system
```

In terms of performance analysis, this means:

* Num of executors we provide >= # of our expected throughput * # of expected latencies 
* Conversely, given the size of resource/thread/conneciton pools, and the average latencies we observce, we can infer our max expected throughput
* Tomcat has default thread pool size of 200. Mysql's `max_connections` defaults to 151. 
