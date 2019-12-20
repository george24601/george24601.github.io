---
layout: post
title: "System design: flash sale"
description: ""
category: 
tags: [interview]
---

### General workflow

* Dedup multiple requests from the same user
  * unique token for each submission request on the frontend
  * redis for consitency check, set 5 mins expire time
  * Use DEL to check if it is dup or not. Successful DEL means it is first time
  * API needs to accept source + seq at the same time, and they should form a unique inde
  * Can also use standard distributed lock
* check storage. User is redirected to waiting page instead of the sales page now
  * optimisitic lock/or distributed lock. Note that pessimistic lock has bad performance
  * storage cache in redis, 5 mins expiry time. Note that we can set the count sightly higher than the actual storage, and reject the out of stock one at the next level
  * MQ 
* deduct storage 
  * If order and then deduct, then people may create many orders without paying
* create order 
  * Cancel order can be done via MQ
  * Status is polling by the waiting page
* pay
  * If no pay by a certain time, release the storage
  * If pay and then deduct, user may order but will not be able to pay

### Rate limiting

* Reduce the traffic layer by layer. Needs to be done on both producer and consumer
  * ngx_http_limit_conn_module, ngx_http_req_module
  * intecetpor at the service + centralized redis counter
* Often need to RL at dynamic hotspots
* Tools:
  1. Token bucket - no need to be producer-consumer, and calcuate last token's timestamp and we can calcualte how many tokens we need to add. Good for blocking case. Good for no SUDDEN change of traffics.Constant incoming rate, stop putting into bucket when the capacity is full. If bucket is full in token Bucket , token are discard not packets. While in leaky bucket , packets are discarded, and can send large burst. The goal is to limit AVG and accept certain bursts
  2. Leaky bucket - unlike token bucket, need to take out token at the rate of t/n. Good for blocking case. less resource utiliation than token bucket, i.e., max output rate, goal is to limit output
  3. Slided time window - slide end, delete obsolete one, and then update counter - but may still have peak traffic in a REALLY small timed window with in the timed window. Good for rejecting rate limiting. Simple
* When hit the rate limit, we need to circuit break
  1. direct reject
  2. add to the a blocking Q
  3. log + warning
* To test: 
  * redirect prod traffic to a small subset of service, and see if the rate limiter is in effect, e.g., even only 1 instance 
  * the shape of the TPS graph should be more reasonable
* Can be deployed at API gateway level or a separate RPC service
