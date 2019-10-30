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
* A consistent read(CR) is done by mult-versioning and reading a snaphost, i.e., NO lock involved
* Gap lock(GL) exists to prevent other txns from inserting into the gap. Therefore, conflicting GL can co-exist, and GL is a concept applied to cluster index
* S, X locks are row level locks (UPDATE/DELETE), but LOCK TABLE..READ and LOCK TABLE ...WRITE are on full table lock

Suppose we have a table t like this

Id | Val
--- | ---
2 | 200
4 | 400

```sql
SELECT * FROM t WHERE Val = 200 -- CR
SELECT * FROM t WHERE Val > 200 -- CR

---- S lock on all rows, GL covering all intervals in (-INF, INF)
SELECT * FROM t WHERE Val = 200 LOCK IN SHARE MOD
---- S lock on all rows, CL covering all intervals in (-INF, INF)
SELECT * FROM t WHERE Val > 200 LOCK IN SHARE MOD 

---- X lock on all rows, gap lock covering all intervals in (-INF, INF)
SELECT * FROM t WHERE Val = 200 FOR UPDATE 
---- X lock on all rows, gap lock covering all intervals in (-INF, INF)
SELECT * FROM t WHERE Val > 200 FOR UPDATE 

SELECT * FROM t WHERE Id = 2 -- CR
SELECT * FROM t WHERE Id > 2 -- CR

-- S on the row, NO gap lock or next-key lock
SELECT * FROM t WHERE Id = 2 LOCK IN SHARE MODE 
-- S on the row, and GL on (2, 4], (5, +INF)
SELECT * FROM t WHERE Id > 2 LOCK IN SHARE MODE 

-- X on the row
SELECT * FROM t WHERE Id = 2 FOR UPDATE 
-- X on the row, and GL on (2, 4], (5, +INF)
SELECT * FROM t WHERE Id > 2 FOR UPDATE 
```

Similarly, for UPDATE and DELETE

```sql
-- record lock because it hits the clustered index
UPDATE t_user set ago = 10 where uid = 1; 

-- table lock(gap lock, X next-key lock) because it does not hit clustered index
UPDATE t_user set age = 10 where uid != 1; 
```

For insert, will use X on the inserted record, but will not lock the range BEFORE this record. At the same time, it will add an insert intention lock, which does NOT block the gap or different key inserting into the same gap, as long as not the same postion within the gap

```sql
INSERT INTO t values (20);
commit;

--txn A
INSERT INTO t values(11)

--txn B
--IIK will be in effect, and B will not be blocked
INSERT INTO t values(12) 
```

Note that concurrent insert is different from concurrent `select for update` + insert. The gap lock will start taking effect!

