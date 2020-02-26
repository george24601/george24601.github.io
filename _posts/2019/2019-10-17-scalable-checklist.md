---
layout: post
title: "Scalable microservice checklist for engineers"
description: ""
category: 
tags: [java]
---

This is based on the internal guide I wrote for Paypay teams. I took out data that are specific to our environment. Almost every item has a SEV 2 behind it.

### Target audience

Backend engineers developing Java microservices

### Java
* GC’s stop the world (STW)  is a common source of CPU spikes and latency spikes. 
  * Most of backend services are IO intensive instead of CPU intensive. In such cases, the CPU usage of process should not exceed 50% of the cores.
  * Use metrics to check both young GC and old GC times and frequency. Frequent young GC is OK. Any sign of old GC requires attention
* STW also means the timer you set at the java side could include the GC time on top of the actual execution time. Therefore, make sure you track client side AND server side processing time at the same time
* Heap size rule of thumb: try to keep it between 4 - 8G, 
  * Rationale: Less than 4G, the survivor region may be too small and gets promoted to old generation too easily. Therefore, it is more likely to trigger old GC
  * Rationale: Greater than 8G, the full GC STW may be too long
* JVM settings
  * Same -Xmx and -Xms
  * For oltp systems, bigger new gen
  * 256MB is more than enough for metaspace
  * 1M max stack size is more than enough

### Network
* Make sure you understand Little's law, and use that to estimate your connection pool size and task queue size
  * Pool’s init/min/max size should be constant, so we can warm up the pool on start
  * On db/server side, prefer smaller connection pool size. Higher than (number of cores * 3) is very debatable. 
     * Rationale: your cores will be saturated with (number of cores * 3) workers already. Adding more workers will only add the context switch cost without adding breathing room for the CPU
  * Set max task queue size. Higher than (num of cores * 10) is very debatable. When the queue is full, prefer abort or discard policy. 
    * Rationale: The system can not process fast enough already, adding in the task queue will not help with the task processing time
* Do NOT make any remote/inter-process calls when you are inside a mutex. 95% of chance such code is wrong
* The network latency between nodes in our DC is about 1-2 ms. Do not issue sequential network call, as the latency will add up quickly
* Low timeout so we can fail early, which also protects the downstream system. Timeout should be no more than 2s on UX-impacting calls (may even consider 1s)

### Operation
* Health check
  * The thread answering health check is often different from the worker thread. To prevent the ninja work done by the worker thread, upon detecting health check failure, make sure you shut down the whole process
  * ELB is default check is 10s time out, and will register the instance as failed after 3 times. This is too generous if the load balancer is within 100 KM of the instances,i.e., failure may not be detected enough
  * When the same region scenario, consider health check timeout of 1s, with 3 as the failure threshould, and 2 as success threshould


### RDS
* Memory should be able to hold all indices. You can estimate the size of indices by (number of rows * avg size of primary keys)
* Try not to use incremental id for PK on big tables, use snowflake/uid generator if possible.
  * Rationale:  strict incremental id generation is NOT support in most of the distributed systems. We will have to use snowflake for id generation when we migrate off RDS anyway.
* Make sure you add quotes when filtering by varchar type. Otherwise, the index on that varchar type will not be hit
* Solving deadlock:
  * On Repeatable-Read isolation level, a common source is the gap lock introduced by batch inserts
  * Check the execution plan of the conflicting queries. Most likely one is holding gap lock or table lock by mistake
* Solving slow queries
  * Check query plan by `explain analyze`. If there is no “range” step, something is wrong. 
  * Note that “index” type step is still a full table scan
  * The most reliable fix is to add an index hint. With it, you don’t even need to worry about the outdated table statistics.

### Redis
* You don’t need redis, if
  * Your distribute lock has no more than 100 rqs - you can just implement the lock in the DB
     * Rationale: trade off between code complexity vs architecture complexity
  * 80% data hits less than 50k entries - you can just use local in-memory cache
     * Rationale: 50k * 1K per entry = 50M memory only
* Redis is single threaded. This means any slower command will block everyone else.
  * For non k-v look up case, make sure your collection size is less than 200 items. Otherwise, you need to redesign your data structure
     * Rationale: avoid the big key problem

### Kafka
* Common kafka use cases:
  * Uses event sourcing or even-driven model or
  * Have to buffer requests to process asynchronously
* People often use Kafka just to partition workloads. Explore other options before opting for kafka. Rationales:
  * Your scaling factor is limited by the number of topic partitions, which is very hard to scale up
  * Very hard to deploy 50+ consumer pods on the cluster, because each Spring pod is a separate process consuming > 1G memory
  * Your true bottleneck is almost always the datasink anyway
* Most producer/consumer logics are at least once, so idempotency should be in your logic all the time
  * Kafka does have exactly once processing, but that works only when both source and sink are kafka topics
  * Most likely you need producer ack = 1
* If you do DB write and kafka write in the same API request, you will need a recon job to recon inconsistencies. Rationales:
  * The DB write and kafka write are not the in the same transaction. So all-or-nothing semantics can’t be guaranteed
  * Reliable message delivery is possible, but not trivial. The effort/reward trade off in our experience is not worth it.
* Replication factor 3 is enough. You can even set it to 2 if losing data is acceptable, e.g., low importance logs
  * Rationale: Kafka deployment is evenly spread across 3 AZs. Replication factor 3 is enough to counter 1 AZ failure

### Spring
* Tomcat + JVM idle will consume almost 1G ram. This, combined with the process context switch costs, means that we prefer bigger but fewer pods
* Spring AOP annotation works only in a @Bean, and only when it is called from another class. We had many cases where @Async, @Transactional annotations have no effect because of these two problems
* Try to limit the @Transactional scope as much as you can, this means
  * Highly debatable to put @Transancatonal scope on the class level. Prefer method level annotation
  * It is normal to create a method just to open the transaction.
  * Rationale: reduce the DB transaction duration as much as we can
* If you use @Async, make sure you override the Executor bean
  * Rationale: default implementation is `SimpleThreadPoolTaskExecutor`, which may create an unlimited number of worker threads
* For OLTP APIs affecting UX, no timeout > 5s, ideally no more than 2s
  * Rationale:if one API can’t finish it under 2s, that means some systems are under stress or faulty already. We fail early to prevent the cascading failure across services
* Prefer native query to JPA/hibernate ORM. Rationale:
  * Often you need to specify index hints
  * Generated query is too long to fit into slow query log. What makes it worse is that the slow query log often truncates the WHERE condition
* Do not include inter-service call inside transaction. Rationale:
  * Reduce DB txn duration as much as we can. This is a common source of deadlock
  * It gives people a false sense of transaction between services when there is none. You have to implement TCC/Saga yourself to have the transaction-ish semantics 
* All calls to third party APIs must be protected by a circuit breaker
  * Rationale: Fail fast, so the worker thread on our tomcat can be freed quickly
