---
layout: post
title: "Spotify Recommendation System Architecture" 
description: ""
category: 
tags: []
---

1. All services write to Kafka and Cassandra

2. Daily recommendation pipeline gets data from HDFS and batch models. It then writes to Cassandra behind services.

3. HDFS has track metadata, play logs, and text such as news and blogs. Play logs also writes to Kafka

4. Probably Kafka to HDFS? 

---
Discussion:


1. Each service reading cassandra must be doing very simple reads, i.e., no crazy analytical-like logics, which is the job of the pipeline


2. For recommendations, cassandra must have all entities and the recommended view. The entities and recommended view will be out of sync for
sure if they are not part of the same table => the recommended view is actually the master view that contains items information, otherwise,
the process logic will have to handle non-existant items case


3. Another option is to have a 3rd entity look up service, better seperation, but a combined recommended view gives better latency due to
less network calls

