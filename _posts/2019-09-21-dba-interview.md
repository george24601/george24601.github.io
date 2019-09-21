---
layout: post
title: "My interview questions for SQL DBA candidates"
description: ""
category: 
tags: [interviews]
---

### Expectations

* Software engineers are responsible for 
 * Design and performance of queries and tables
 * Basic DB topology design, including the placement of DB servers
 * Monitoring and alerting metrics from the application code, but not DB
* DBAs are responsible for 
 * Monitoring and alerting metrics from DB layer
 * Backup and restore db from/to different servers
 * Failover/failback drill
* DBA should be able to answer at minimum 80% of questions I posted here
* Communication wise, see if they are aware and follow the STAR principle
* Culture wise, see if they demonstrate
  * Ownership
  * Disagree and commit
  * Dive deep

### Questions 
* Give two cases of deadlocks you see on the DB, and explain how you solved it.
  * Be able to explain isolation levels
  * Be able to explain difference of different lock types
  * Be able to explain ways to detect and fix deadlock cases
* Give two cases of slow queries you see on the DB, and explain how you solved it
  * Show the use of slow query log, query plan, and unix performance debugging commands
  * Monitoring and alerting should be in place to discover the slow query after 5 mins for OLTP, or high resource usage for OLAP
  * Be able to explain the common patterns of setting up indices and identify when the indices are too many or too few
* Give two cases of replication lag you see on the DB, and explain how you solved it
  * Be able to explain replication lag threshold set, which should be no more than 10 minutes
  * Be able to explain the HA and DR setup of the replication tool, and the operational excellence to ensure the HA
  * Be able to give verfication procedure after the replication is fixed
* What is the backup/restore DB speed I can expect?
  * Be able to dig more context info without being prompted. Red flag if jump into an estimate immediately
  * Be able to give verification procedure after the restoration is done
  * Be able to explain common parameters for the dump/restore tool
* Design or explain DR drills for the DBs you are responsible for
  * Knows the meaning of RTO, RPO, MTTR. Red flag if not able to specify them before jumping into drill details
  * The DR drills should cover multiple cases, from the most simple to complete outage. At least 3 cases should be covered




