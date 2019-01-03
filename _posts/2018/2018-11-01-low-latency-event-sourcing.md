---
layout: post
title: "Design Discussion: Low Latency Event Sourcing in Microservice" 
description: ""
category: 
tags: [microservice]
---

### Scenario

* In a domain event, Service A persists one domain change to MySQL database and publishs the event DTO to Kafka, which acts as the event bus. Service B listens to Kafka for the event DTO.
* Reliability requirement: one domain change persisted should cause at least one DTO published to Kafka eventually,i.e., no lost update to Kafka or MySQL
* Latency requirment: The 99th percentile time between Service A starting the persistence and Service B receiving the message should be < 1s.
* Throughput requirement: 500 msg/s. This means no distributed transactions such as 2PC.
* Following the original BASE paper, service A can have a log table so that DTO can be persisted in the same transaction as the domain change. 

### Option 1: CDC/binlog tailing

* CDC tool will listen to the log table and write to kafka
* Latency of MySQL -> CDC replication is unknown, but it has good throughput, we achieved easily 10k records/sec in production in the past project.
* Latency of CDC -> Kafka replication is reliable - 99th percentile < 5ms. Even a single kafka producer has good throughput with ack and sync rep turned on - easily 10k msg/sec
* Need deep understanding of MySQL replication techiques and mechanisms to troubleshoot
* The low latency requirement means we need to setup CDC tool's HA, e.g., via Zookeeper's ephemeral code

### Option 2: Compensating transaction + polling

* Following Ebay's BASE paper, service A writes to log table and domain table in the same transaction
* After the transaction commits, A will asyncly send the DTO to kafka, and upon sendng successful, A will update the entry to log table status to published, or delete the record 
* At the same time, we have log cleaner daemon process, which will clean up the old logs and re-publish logs not marked as published
* Note that the async call means that in a single domain flow, two DTOs may arrive at the kafka out of order, because they are from two different processes of the same service A. It really depends on the actual logic to figure it out         

### Option 3: Pure polling

* Re-invent the MQ semantics with the log table. Testing on our laptop shows that log table can easily handle 2k writes/sec
* This also suggests that we need to add virtual partition so that we can have multiple polling messaging publisher work at the same time. We also need to include the consumer group maintainence mechnism akin to those in Kafka


