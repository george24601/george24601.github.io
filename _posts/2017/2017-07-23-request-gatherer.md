---
layout: post
title: "System design: request log gatherer and analyzer "
description: ""
category: 
tags: [systemDesign]
---

Design a dapper-like system for tracing and monitoring
1. Have the ability to drill down a certain request and see its sequence of actions

2. Have the ability to run analytical queries to see average latency, tail latency, latency by the machine/cluster etc

Design
--------
We need to generate a request id, for each request, and session id
The request can be embedded in the header

WHen we forward the RPC calls, the session id should be forwarded, as long with the parent request id

In the end we will have a tree of requests

On client side, we should do uniformed random sampling based on session id. (0.1% is good enough)

On the service side, we can maintain a distributed log to accept all incoming requests. This log will go to two sinks

One a k-v store that keyed by session id, column name by request id/name

one to a simple HDFS sink for full-table scan analytical queries. This HDFS sink is also useful in case of loss of data

Q: do we need aggregated view of acorss all DCs?

Then we need aggregated log and run populate k-v store and  HDFS


How long we want to keep the data?
1. Data in HDFS can be partitied by time and thus easily purgable

2. Can do a FTS on k-v store every 4 weeks or reaches a certain thresold, e.g., you know half of that is redundant anyway
