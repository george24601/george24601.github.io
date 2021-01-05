---
layout: post
title: "ElasticCache redis catches" 
description: ""
category: 
tags: [redis]
---

### Losing data on rebooting primary in clustered mode

* If we reboot the primary, both primary and its read replica/slave's data will be wiped
  * Reboot takes about 2 mins from start to primary health check passed again. Auto failover will not be triggered in this process
  * Note that if we deploy redis on EC2 directly, we can turn on AOF, so that on server reboot, all data upto the last fsync will be in memory. However, EC redis disabled AOF in cross-AZ mode and cannot be turned on! This suggests a risk of cache avalanche and stakeholders need to eval the risk/reward ratio
* If we reboot the read/replica, the primary data remains intact
* Promoting the replica to master will keep the data in the new master and old master intact

