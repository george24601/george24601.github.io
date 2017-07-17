---
layout: post
title: "How Oceanbase implements tablet location resolution"
description: ""
category: 
tags: [archtecture]
---

Reference
https://github.com/alibaba/OceanBase-0.5/blob/master/doc/OceanBase%200.5%20SQL%20%E5%8F%82%E8%80%83%E6%8C%87%E5%8D%97.pdf

See figure 2-1 on page 2

Note that OB's tablet resolution is very similar to bigtable's. DNS uses a similar layered approach.

Structure
---------
0. Tables in OB (and bigtable) is partitioned into a list of tablets. Rows inside a table are sorted by row key, and there is no overlap between keyranges of different tablets.
Together these tablets form a sorted view of the table without any duplicate data. Each tablet by default is a 256MB file.   

1. Each table create by user has multiple such tablets. Therefore, for each user table, we need a User Meta Table (UMT) to keep track of the location of tablets. Each row in UMT stores the list of chunkServers with replica of a single tablet. Rows are keyed by the keyrange of the tablet (startKey, endKey]. UMT itself is read/written same way as normal user table

2. Since user table can be tens of TBS, often UMT itself might be bigger than 256MB. Assume each row takes 1 KB, then a single tablet can hold 256 * 2^20 / 2^10 = 2^18 rows. 
2^18 rows in UMT in turn maps to 2^18 * 256MB  = 64TB in the actual user table. This means UMT itself needs to be stored in multiple tablets, and we need to introduce another layer to keep track of UMT's tablets

3. To keep track of UMT tablet locations, OB uses one User Root Table (URT) for each UMT (and in effect one URT for each user table), each row is list of chunkServers that holds a URT replica, keyed by the range of tablet (startKey, endKey]. Given the calculation we did in 2), we know 1 tablet is enough for URT, and thus OB can cache URT's content in memory. Again URT is r/w same way as regular user table

4. Now we need a centralized location to location all UMTs, such table is called First Root Table (FRT). Each row stores the locations of UMT replicas. Again, given the calculation in 2) we can see one tablet is enough for FRT, and OB will always cache this table in memory. OB will write the locations of FRT replicas in a pre-defined location so other servers can know where to ask on bootstrap 

Note, location resolution for other OB system tables is slightly different, but they are generally small and is beyond the scope of the discussion

Performance
---------
For uncached location resolution, we need make 3 calls (FRT -> URT -> UMT) to find the location of tablet. These remote calls are always within the same cluster, so 99% percentile latency should be around 0.5ms * 3.

Again, similar to DNS, all client/servers caches location resolution results to avoid these 3 roundtrips 
