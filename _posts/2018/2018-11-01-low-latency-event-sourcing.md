---
layout: post
title: "Design Discussion: Event Sourcing" 
description: ""
category: 
tags: [microservice, arch]
---

### Scenario

* In a domain event, Service A persists one domain change to MySQL database and publishs the event DTO to Kafka, which acts as the event bus. Service B listens to Kafka for the event DTO.
* Reliability requirement: one domain change persisted should cause at least one DTO published to Kafka eventually,i.e., no lost update to Kafka or MySQL
* Latency requirment: The 99th percentile time between Service A starting the persistence and Service B receiving the message should be < 1s.
* Throughput requirement: 500 msg/s
* Following the original BASE paper, service A can have a log table so that DTO can be persisted in the same transaction as the domain change. 
* CDC/binlog is not feasible because we have mutli-row, multi-table txns

### Option: Compensating transaction + polling

* Following Ebay's BASE paper, service A writes to log table and domain table in the same transaction
* After the transaction commits, A will asyncly send the DTO to kafka, and upon sendng successful, A will update the entry to log table status to published, or delete the record 
* At the same time, we have log cleaner daemon process, which will clean up the old logs and re-publish logs not marked as published
* Note that the async call means that in a single domain flow, two DTOs may arrive at the kafka out of order, because they are from two different processes of the same service A. It really depends on the actual logic to figure it out         

### Domain event format

* PK
* OP
* State before the change
* State after the change
* src metadata
  * src log/db/tbl
  * src timestamp (event time)
* capture time (processing time)

Inside DB, need message id , and message status (new, published). Debatable if we want different messages go into the same log table. If we need it, then a separate message type column is required

### Case study: [spring-domain-events](https://github.com/odrotbohm/spring-domain-events)

* DB Schema:
  * msg id
  * msg pub date
  * listener id
  * seriaized string
  * event type
  * completion date 
* Problems with this repo in a production setting
  * The recovery didn't consider the case of concurrent recovery
  * The recovery can be triggered only at `afterSingletonsInstantiated`, i.e., lacks a separate, continuous recovery process
  * The recovery query itself is just a full table scan
  * Persisting the event happens outside the same db transaction, i.e., with TransactionalApplicationEvent. Therefore, it is still possible that domain change is done, but event is lost



