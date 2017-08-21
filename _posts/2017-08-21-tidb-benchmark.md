---
layout: post
title: "My TiDB benchmark runs"
description: ""
category: 
tags: [architecture]
---

Document results here since there is not many English sources on this

Setup
-------------
```
6 r3.2xlarge (8vCPU, 61 G ram). 3 act as PD/TiDB, 3 act as TiKV. All TiDB/TiKVs run on mounted instance storage SSD

2 c4.2xlarge (8vCPU, 15G ram) as test driver servers.
```

All 8 servers are within the same availability zone. Ping between each server is around 0.15ms


Sysbench: Oltp_read_only
---------
A. Prepare data: 16 tables, each with 1M rows
```
./sysbench ./lua/oltp_read_only.lua         --mysql-host=$TIDB1 --mysql-port=4000 --mysql-user=root --mysql-password=""         --mysql-db=test --tables=16 --table-size=1000000         --report-interval=10         --threads=10 --time=0  prepare
```

B. on the first test driver server run
```
./sysbench ./lua/oltp_read_only.lua         --mysql-host=$TIDB1 --mysql-port=4000 --mysql-user=root --mysql-password=""         --mysql-db=test --tables=16 --table-size=1000000         --report-interval=10         --threads=10 --time=0         run
```

C.on the second test driver server run
```
./sysbench ./lua/oltp_read_only.lua         --mysql-host=$TIDB2 --mysql-port=4000 --mysql-user=root --mysql-password=""         --mysql-db=test --tables=16 --table-size=1000000         --report-interval=10         --threads=10 --time=0         run
```

Note that the two test servers use different --mysql-host in an attempt to balance network traffic on TiDB/PD servers

Result:
Combined QPS from the test servers around 12.5 k. 
95% latency between 25-39 ms

Sysbench: Oltp_insert
---------
A. Prepare data: same command as oltp_read_only: 16 tables each with 1M rows

B. on the first test driver server run
```
./sysbench ./lua/oltp_insert.lua         --mysql-host=$TIDB1 --mysql-port=4000 --mysql-user=root --mysql-password=""         --mysql-db=test --tables=16 --table-size=1000000         --report-interval=10         --threads=20 --time=0         run
```

C. on the second test driver server run
```
./sysbench ./lua/oltp_insert.lua         --mysql-host=$TIDB2 --mysql-port=4000 --mysql-user=root --mysql-password=""         --mysql-db=test --tables=16 --table-size=1000000         --report-interval=10         --threads=20 --time=0         run
```

Again, the two test servers use different --mysql-host in an attempt to balance network traffic on TiDB/PD servers

Result:
Combined TPS from the two test servers around 4.5 k. 
95% latency around 14 ms

Findings from sysbench runs
-----------
1. When I run the same commands with --range_selects=false, the combined qbs is around 17k, with 95% latency between 14-22 ms

2. Among the 4 range queries in oltap_read_only.lua, only execute_distinct_ranges is slower. Such behaviour is expected, given the distinct_ranges query.

3. The latency/QPS improves almost linearly as I take out range queries one by one, which is expected


TPCC: latency test
----------
I populate data with the simplistic load
```
./tpcc_load -h 127.0.0.1 -P 4000 -d tpcc1000 -u root -p "" -w 1
```

and run a simple test
```
./tpcc_start -h 127.0.0.1 -P 4000 -d tpcc1000 -u root -w 1 -c 1 -r 10 -l 40
```

Results
---------
```
 10, trx: 80, 95%: 59.947, 99%: 62.177, max_rt: 62.718, 80|54.310, 8|61.505, 8|144.893, 9|304.212
  20, trx: 86, 95%: 59.696, 99%: 60.778, max_rt: 61.960, 83|55.630, 8|59.896, 8|142.030, 8|290.288
  30, trx: 81, 95%: 59.911, 99%: 62.270, max_rt: 63.184, 85|55.237, 9|61.069, 8|144.407, 8|300.596
  40, trx: 74, 95%: 61.437, 99%: 62.344, max_rt: 64.457, 75|54.663, 7|58.993, 8|145.278, 9|308.553
```


Findings from TPCC runs
------------
1. when I deploy 3 instances across AZs but within the same DC, 95% percentile jumps to about 110ms

2. Because of MVCC, if number of warehouses is too low (< 10 * number of threads) we will see unable to update error from time to time

3. Even though TPCC is estabilished bench mark, both AliSQL and TiDB primarily use sysbench
