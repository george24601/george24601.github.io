---
layout: post
title: "How percolator/tidb transaction works"
description: ""
category: 
tags: [tidb]
---

Uses a timestamp oracle(TO) to allocate monotonically increasing TS. Every txn needs to contact TO twice, one for start_ts at the beginning of prewrite, one for commit_ts at the beginning of commit

Each logical k-v has 3 column families(CF):
* Data: key_starTs-> value. Stores the actual data
* Lock: key -> meta: Stores txn lock infos
* Write: key_commitTs->startTs: The value at (Data)key_commitTs is stored at (Data)key_startTs

### Sequence of actions

Initially Bob has $10, and Joe has $2

So the k-vs at the start of the transaction are:

```
(Data)Bob_5: 10
(Data)Bob_6: null
(Lock)Bob_5 : null
(Lock)Bob_6 : null
(Write)Bob_5: null
(Write)Bob_6: data@5

(Data)Joe_5: 2
(Data)Joe_6: null
(Lock)Joe_5 : null
(Lock)Joe_6 : null
(Write)Joe_5: null
(Write)Joe_6: data@5
```

Note that write keys at version 6 point to the data key at version 5. This is the end state of previous transaction

Now Bob wants to transfer $7 to Joe
1. Gets the start_ts at 7 from TO, and write (Data)Bob_7:3 
2. Write (Lock)Bob_7: Primary. This marks this key is THE primary lock of the whole txn, regardless of how many keys are changed in this txn 
3. Write (Data)Joe_7: 2, and (Lock)Joe_7:Primary@Bob. The new lock entry marks the secondary lock that is used to look up for the primary lock. Now prewrite step is done!
4. Gets the commit_ts at 8 from TO, and write (Write)Bob_8:data@7
5. Delete (Lock)Bob_7
6. At this stage we think the txn is complete already! Even if we have not clean up for Joe's entries yet. In tidb, the below steps will process asyncly. See twoPhaseCommitter.doActionOnKeys
7. Check (Lock)Joe_7, find it points to (Lock)Bob_7, which is deleted already, so we know the txn is complete already, and delete the (Lock)Joe_7
8. Write (Write)Joe_8: data@7


Final shape

```
(Data)Bob_5: 10
(Data)Bob_6: null
(Data)Bob_7: 3
(Data)Bob_8: null
(Lock)Bob_5 : null
(Lock)Bob_6 : null
(Lock)Bob_7 : null
(Lock)Bob_8 : null
(Write)Bob_5: null
(Write)Bob_6: data@5
(Write)Bob_7: null
(Write)Bob_8: data@7


(Data)Joe_5: 2
(Data)Joe_6: null
(Data)Joe_7: 9
(Data)Joe_8: null
(Lock)Joe_5 : null
(Lock)Joe_6 : null
(Lock)Joe_7 : null
(Lock)Joe_8 : null
(Write)Joe_5: null
(Write)Joe_6: data@5
(Write)Joe_7: null
(Write)Joe_8: data@7
```



