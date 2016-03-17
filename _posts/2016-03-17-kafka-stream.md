---
layout: post
title: "Notes on Kafka Stream"
description: ""
category: 
tags: [stream-processing]
---

less on analystics, more on apps and services that process data streams

most valuable streaming apps is closer to async microservice => implement core functions rather than computing analytics

goal: use mainstream application programming model for asynch services

no cluster manager, just a library

load balancing: same protocol that Kafka provides for normal consumers

KStream and KTable can be converted back and forth

How to do windowed operations on streams if events can arrive out of order => represnet count so far for the window. continously update as
new data arrives and allow downstream receiver to decide when it is complete

assigns ts to every data record=> event time or processing time possible
