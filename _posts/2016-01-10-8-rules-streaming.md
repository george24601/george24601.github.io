---
layout: post
title: "8 Rules for Stream Processing"
description: ""
category: 
tags: [stream-processing]
---

Active system. No costly storage op in processing
----------
System must perform message processing w/o costly storage operation in the critical processing path.

Passive systems require applicaitons to poll for conditions of interest, but half the polling interval is added to the processing delay=>
expected time of next arriabal. So the system should be event-driven, active system

High-level, extensible processing language
----------
StreamSQL should extend the sematics of standard SQL by adding to it rich windowing constructs and stream-specific operators. window serves the purpose by defining the scope of a mult-message operator,i.e., support a high-level language with  built-in extensible stream-oriented primitives and operators

infrastructure must handle data that is late or delayed, missing, or out-of-sequence. Every calculation that can block must be allowed to time out

must guarantee predicatble and repeatable outcomes

Efficiently store, access, and modify state information
----------
seamless switching,i.e., without code change, "catch up"-> switch automatcially from historical to live data, without the maunal
intervention of a human

state must be stored in the same OS address space as the application using an embedded DB system. For seamless integrateion, the system
should use a uniform language when dealing live stearming data and state information

Tandem-style hot backup and real-time failover scheme=> HA

Application should automatically and transparently load-balance over the avaialble machines

highly-optimized, minimal-overhead execution engine that minimized ration of overhead to useful work, and limit "boundray crossing" by
integarting all intoa single system process.
