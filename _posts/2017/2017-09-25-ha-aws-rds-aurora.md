---
layout: post
title: "On HA of AWS Mysql RDS and Aurora"
description: ""
category: 
tags: [database, architecture, aurora]
---
MySQL RDS
-------
In multi-AZ deployments for MySQL, data in primary DB is synchrously replicated to a standby instance in a different AZ. Failover is automatic, and endpoint remains same. Backup is taken from the standby, so that I/O is not suspendend on primary

Note the read replica, a different concept from the multi-az replica, uses MySQL's async replication


Aurora RDS
--------
Storage is handled by the storage layer,i.e., shared-disk approach, and is replicated across 3 AZs, each with 2 copies. 

Aurora uses a SSD-backed virutalized storage layer.  

Each write has to be acknolwedged by 4 out of 6 copies (sync), while read covers 3(4 + 3 > 6). This is to ensure
1. If 1 AZ + 1 machine is down, can still read
2. If lost 2 replicas, even if they are in the same AZ, can still write

For each source DB cluster, you can only have one cross-region Read Replica DB cluster. Data transfer between Regions incurs extra charge. 

It is possible to promote the cross-region to standalone cluster, but this also means we need to add a cross-region replica to this newly promoted master - the old master will be on a split brain, and can not be re-incorporated!

Also note that for EACH region, aurora gives 2 end points, one read point, and one write end point. It is not smart enough to do auto-load balance!
