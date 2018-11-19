---
layout: post
title: "InnoDB lock behavior under repeatable read"
description: ""
category: 
tags: [InnoDB, interview]
---

* By default InnoDB is on repeatable read isolation level.
* S - shared locks, X - exclusive locks
* The actual lock is on the clustered index, in most cases this means the PK index.
* A consistent read(CR) is done by mult-versioning and reading a snaphost, i.e., no lock involved
* Gap lock(GL) exists to prevent other txns from inserting into the gap. Therefore, conflicting GL can co-exist, and GL is a concept applied to cluster index

Suppose we have a table t like this

Id | Val
--- | ---
2 | 200
4 | 400

```sql

SELECT * FROM t WHERE Val = 200 -- CR
SELECT * FROM t WHERE Val > 200 -- CR

SELECT * FROM t WHERE Val = 200 LOCK IN SHARE MOD ---- S lock on all rows, GL covering all intervals in (-INF, INF)
SELECT * FROM t WHERE Val > 200 LOCK IN SHARE MOD ---- S lock on all rows, CL covering all intervals in (-INF, INF)

SELECT * FROM t WHERE Val = 200 FOR UPDATE ---- X lock on all rows, gap lock covering all intervals in (-INF, INF)
SELECT * FROM t WHERE Val > 200 FOR UPDATE ---- X lock on all rows, gap lock covering all intervals in (-INF, INF)


SELECT * FROM t WHERE Id = 2 -- CR
SELECT * FROM t WHERE Id > 2 -- CR

SELECT * FROM t WHERE Id = 2 LOCK IN SHARE MODE -- S on the row
SELECT * FROM t WHERE Id > 2 LOCK IN SHARE MODE -- S on the row, and GL on (2, 4], (5, +INF)

SELECT * FROM t WHERE Id = 2 FOR UPDATE -- X on the row
SELECT * FROM t WHERE Id > 2 FOR UPDATE -- X on the row, and GL on (2, 4], (5, +INF)
```
