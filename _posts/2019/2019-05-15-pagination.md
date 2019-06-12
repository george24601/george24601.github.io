---
layout: post
title: "Design Problem: Pagination"
description: ""
category: 
tags: [architecture, interview]
---

### Problem
1. composite PK for message (msg_id, order_id, post_id)
2. We want to paginate by the time
3. data is sharded across DBs
4. partition key is id % shards to ensure even distribution among shards
5. We want to be able to go to a SPECIFIC page with known page size.

Of course complete view can do that, but can we do better?

### 100% accurate: next page only
1. From product PoV, allows only next page, instead of going to the specific page. 
2. Every page we fetch, we remember the end of the timestamp at the current page
3. When we look for the next page, we just use that last timestamp to pull the full page from each shard, and aggregate them in memory

### Mostly accurate: guess the shard 
1. We just assume the data is evenly distrubuted
2. Therefore, for each specific page we can compute exactly the offset, and page size. Both will be the original size/number of shards

### 100% accurate: good perf but more complicated
* Similar to the random partition method, pull pages from sharded db based on guessed offsets, but real page number, call the returned page RS
* Find the min_time of all 3 pages returned
* Query again with the time between min_time and the max time returned by each page in RS
* Find the virtual offset of min_time of in each shard's page, and now we know the exact offset of min_time from the global PoV. Note that min_time global offset will be less than the target offset for sure.
* Now that with global view, we have enough information in memory to calculate the real offset from each record
