---
layout: post
title: "Prometheus percentile misconceptions"
description: ""
category: 
tags: [prometheus]
---

Reading notes on [this post](https://mp.weixin.qq.com/s/f81MzD_MroAYxrO6Z_gHWQ)

The key idea is that the most important thing about a metric is its distribution, and P99 does not say anything about the remaining 1% and previous 98%. Therefore, we can construct many counter-intutitive cases, and normally can not apply transitivity and composition directly

### Suppose P(99,X) = 100ms, X consists of two sequential steps A and B, P(99,A) = 60ms 

* Can P(99,B) > 100 ms? No
* If slowest 1% of A is matched with slowest 1% of B, then we can conclude P(99,B) = 40 ms
* Can P(99,B) in (40, 100] ms? Yes
  * suppose P(98,A) = 1ms, P(100,A) = 60ms, P(100,B) = P(98,B) = 99ms, P(1, B) = 1ms
  * Then we can constuct the distribution as P1(X) = 61ms, P(99,X) = 100ms, P(100,X) = 159ms
* Can P(99,B) in (0, 40) ms?
  * Yes, suppose P(99,B) = 1ms, P(100,B) = 40ms, P(99,A) = P(1, A) 60ms, P(100,A) = 99ms
  * Then we can construct P(99,X) = P(100,X) = 100ms, P(98, X) = 61ms

### X consists of two sequential steps A and B, P(99,A) = 60ms, P(99,B) = 40ms
* Can P(99,X) > 100ms? 
* Can P(99,X) be less than 95ms?
* Yes to both cases, just use the two constructions we have in the last sections

### X sends requests to M, which will batch requests into a single DB request
* Can P(99,X) > P(99,M)? Yes, due to batched waits
* Can P(99,M) > P(99,X)? No, the only thing we can confirm is P99 of composition >= P99 of parts

### In P*
* P* only records count and sum of buckets. 
* If max bucket is too small, qunatile will record only the max bucket. The end effect is a straight line
* If bucket range is too big, too many data will fall into the same bucket, with the assumption that they are evenly distributed within the same bucket
