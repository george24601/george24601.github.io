---
layout: post
title: "System design: flash sale"
description: ""
category: 
tags: [interview]
---

General workflow: check storage -> deduct storage ->  create order -> pay

Dedup multiple request from the same user
1. redis for consitency check, set 5 mins expire time
2. unique token for each submission request on the frontend

Use MQ to run steps asychly as much as we can
* Cancel order can be done via MQ
* Also works as storage check solution
* Rate limiting needs to be on both producer and consumer

Check storage options:
* optimisitic lock
* storage cache in redis

Deployment options
1. API Gateway
2. rate-limiter as RPC service
3. within the microservice systems


### Rate limiting

Reduce the traffic layer by layer
* Tools:
  1. Token bucket - no need to be producer-consumer, and calcuate last token's timestamp and we can calcualte how many tokens we need to add. Good for blocking case. Good for no SUDDEN change of traffics.Constant incoming rate, stop putting into bucket when the capacity is full. If bucket is full in token Bucket , token are discard not packets. While in leaky bucket , packets are discarded, and can send large burst. The goal is to limit AVG and accept certain bursts
  2. Leaky bucket - unlike token bucket, need to take out token at the rate of t/n. Good for blocking case. less resource utiliation than token bucket, i.e., max output rate, goal is to limit output
  3. Slided time window - slide end, delete obsolete one, and then update counter - but may still have peak traffic in a REALLY small timed window with in the timed window. Good for rejecting rate limiting. Simple
* When hit the rate limit, we need to circuit break
  1. direct reject
  2. add to the a blocking Q
  3. log + warning
* To test: redirect prod traffic to a small subset of service, and see if the rate limiter is in effect, i.e., the shape of the TPS graph should be more reasonable


### Idempotency
* Have a token on the request, use redis to check
 * Use DEL to check if it is dup or not. Successful DEL means it is first time
 * TTL 5 mins. Token can be generated on the service side before the data is submitted
* Distributed lock
* API needs to accept source + seq at the same time, and they should form a unique inde
