---
layout: post
title: "Process for infra cost control"
description: ""
category: 
tags: [java, interview]
---

### Product engineering team
* Anyone in the product team can request resources.
* Use the guideline below to request infra resource. 
* Any resource request higher than the guideline needs to be back by data or concrete reasons. Gut feeling is not acceptable.
* The infra team will challenge the product team on costs. 
  * Three strikes for overprovisions. Upon the third strike, the product team must provide the post mortem on why over-provision happens so often and publish it to all tech teams
* If the product team is not able to convince infra team, escalate to the team lead and then senior tech lead

### Infra engineering team
* The infra team is responsible for challenging the product team to bring down the cost.
* Every successful challenge and potentially saved cost should be logged and recorded as part of the KPI. This number will be published to the management monthly or quarterly.
* Use the guideline below to request infra resource. 
* Any resource request higher than the guideline needs to be back by data or concrete reasons. Gut feeling is not acceptable
* If the product team is not able to convince infra team, escalate to the team lead and then senior tech lead

### Guidelines
* Overall, if cost is the same, prefer bigger but fewer instances, e.g., provision one c4.8xlarge instead of two c4.4xlarge. 
  * Reduces the probability that any given instance failed
  * Reduces the noisy neighbor problem commonly experienced in virtualized environments
  * Reduces the per-host monitoring effort, both effort and money-wise.
* Computing instances for k8s
  * Instance type defaults to c5.9xlarge. 
  * Any instance request less than c5.4xlarge should be extremely rare, e.g., once per month.
* Number of replicas: 
  * Each tomcat instance should handle between 200 to 2000 RPS. 
      * This means very unlikey you need more than 20 replicas for a single services
      * Use Little's law to estimate. Rule of thumb is 1k RPS per replica
  * Each kafka consumer should be able to handle at least 200 msg/sec. In general the consumption is not the bottleneck, the data sink is. 
    * For OLTP workload, highly unlikley you need more than 3 consumers.
* RDS/Aurora
  * Overprovisioning write db is OK, because it is hard to scale up. 
    * For high traffic OLTP services (> 1000 rps peak), r5.4xlarge is more than enough
    * For internal services OLTP (less than 100 rps), m5.2xlarge is more than enough
  * Read db is needed only for high traffic OLTP services, max 3 read dbs are enough.
  * If the suggested db specification is not enough, then 80% it is code or architecture problem
* Redis
  * One r5.4xlarge should be enough for almost all our use cases, i.e., 20k rps.
  * If the redis expects to handle no more than 200 rps, then we don't really need to redis. Just use DB
  * Replication factor 2 is enough in 80% cases, although 3 is OK
* Elasticsearch
  * Keep between 10% to 30% free storage size. Higher than 30% means over-provision
  * For log analysis, defaults to m5.4xlarge, with 2TB storage per node
