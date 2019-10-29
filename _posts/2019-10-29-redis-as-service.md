---
layout: post
title: "Redis as a service at weibo"
description: ""
category: 
tags: [interview]
---

* Latency requirement: 4 9s at 20ms
* Config service to store config, and client to read from it
  * Cluster manager manages the config service, which in turn is subscribed by the proxy
* Proxy layer to route request to the clusters behind
  * Listent o port, anaylze the Redis protocol traffic, and then route to backend based on the (port, key, op)
  * Also subscribes to the config change
* 99% multi-layer cache hit rate
* Twitt publication is async, peak is evened out by the queue
* Cluster management process
  * Resource request: service id, data size, traffic size, data type
  * Resource allocation: cluster id, proxy, service, monitor & alerting
  * Service online: domain
  * Resource check
  * Resource change: scale out, failover
