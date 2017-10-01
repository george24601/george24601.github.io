---
layout: post
title: "Distributed Systems Testing - from TiDB talk"
description: ""
category: 
tags: [system]
---

[Original in Chinese](https://www.pingcap.com/blog-distributed-system-test-%7C%7C-zh)

3. Profile everything, even on production - to catch once in a life time bug

4. Tests may make your code less beautiful - May need to add members to structs just for testsings, but we still need to design for tests

5. Fault injeciton: to test network failure case, try automate it without human intervention - Otherwise it is inefficient

6. Disk fails > 8% after 3 years

6. Importatnt to monitor NTP, detect jumping back => normally bad!!

7. Reading data from disk without checksum => no protection against potential data corruption

8. Fault injection: disk error, netowrk card, cpu, clock, file system, network & protocol => need to simulate everything so that you can inject error

9. Common tools: libfiu, openstack fault injection factory, Jepsen (mostly famous), 

10. FoundationDB limitation: fake multi-process does not work well with languages where single thread is in effect multi-threaded,e.g., channel)

11. TiKV uses namazu. Planning to introduce OpenTracing (in Go) to fill the similar role as Google Dapper

12. Dont test failure case by triggering failure automatically, use your simulation layer 
