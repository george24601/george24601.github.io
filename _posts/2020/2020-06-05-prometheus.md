---
layout: post
title: "Common prometheus problems" 
description: ""
category: 
tags: [prometheus]
---

### Range vector

* With counter type, the it is more common to use rate() rather than increase(), because the latter is changed by the length of the bucket, i.e., it will compute the different between values at the both ends of the bucket, so longer the bucket bigger the value
* For the same reason above, don't use rate() or increase() over gauge metrics because the underlying assumption does not match - Counter is always increasing, while gauge can go up and down
  * Any decrease in counter is treated as counter reset
* Because of reset and extrapolation logic, recommend to set time interval 5 times of the scrapping interval. So we we are resilient enough to have 2 values in the bucket


### Agents behind LB
* We need to expose all agents so that they can be scrapable by the P server
* If the above requirement is hard, then we need addtional sidecar/proxy on each node. Alternatively, add separate URL path for each server to the LB 
