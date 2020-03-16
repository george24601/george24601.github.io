---
layout: post
title: "double write + topic without parition keys = no total order guarantee"
description: ""
category: 
tags: [kafka]
---

A problem I ran into at work

* Each consumer does two things, write to A, and then write to B
* The source topic has no parition key defined, i.e., the input messages are randomly sent to different paritions

Now consider this sequence of actions

* Consumer 1 writes to A
* Consumer 2 writes to A
* Consumer 2 writes to B
* Consumer 1 writes to B

* The data sink A sees two messages in the order: 1, 2
* The data sink B sees two messages in the order: 2, 1
* Note that A and B receive different orders even though each consumer preserves it! This means operation has to be communtative or data sink A and B will give different results!

