---
layout: post
title: "What triggers the txnLockFast failure?"
description: ""
category: 
tags: [tidb]
---

Source version 2.1

### What is txnLockFast error

This is triggered when read txn with read_start_ts happens between the write txn's write_start_ts and write_commit_ts. Upon seeing this, the read txn will backoff retry later witht the same read_start_ts, so that we preserve snapshot isolation. The effect of txnLockFast is similar to pessimistic lock but with higher latency

### tidb_tikvclient_backoff_seconds_count

Triggered by expression `increase( tidb_tikvclient_backoff_seconds_count[10m] )  > 10` 

`tidb_tikvclient_backoff_seconds_count` is a range vector which contains counters. This expression means it happensin more than 10 of the seconds in the last 10 minutes 


### Data structures

Retriever is the interface wraps the basic Get and Seek methods. Retriever is implemented by BufferStore -> unionStore -> tikvSnapshot -> tikvTxn 

Transaction defines the interface for operations inside a Transaction. `Transaction` is implemented by tikvTxn -> TxnState

```go
// Scanner support tikv scan
type Scanner struct {
	snapshot     *tikvSnapshot
	batchSize    int
	valid        bool
	cache        []*pb.KvPair
	idx          int
	nextStartKey []byte
	endKey       []byte
	eof          bool

	// Use for reverse scan.
	reverse    bool
	nextEndKey []byte
}
```

### What triggers txnLockFast?

* Commit check
  * `TxnState` also implements `BatchGet` but seems not used.
  * `tikvTxn` also implments `Get` but not used except for testing

```go

//part of Retriever interface
func (us *unionStore) Get(k Key) ([]byte, error) {
  us.markLazyConditionPair(k, nil, e.(error))
}

//part of Transaction interface
func (txn *tikvTxn) Commit(ctx context.Context) error {
 txn.us.CheckLazyConditionPairs()
}

func (us *unionStore) CheckLazyConditionPairs() error {
 us.snapshot.BatchGet(keys)//this may trigger txnLockFast
}

```
* Scan keys in tikv
 

```go

func (s *Scanner) Next() error {
   s.resolveCurrentLock(bo, current)
}

func (s *Scanner) resolveCurrentLock(bo *Backoffer, current *pb.KvPair) error {
  s.snapshot.get(bo, kv.Key(current.Key))
}

```

* Coprocesser

```go

// handleCopResponse checks coprocessor Response for region split and lock,
// returns more tasks when that happens, or handles the response if no error.
// if we're handling streaming coprocessor response, lastRange is the range of last
// successful response, otherwise it's nil.
func (worker *copIteratorWorker) handleCopResponse(bo *Backoffer, resp *copResponse, task *copTask, ch chan<- *copResponse, lastRange *coprocessor.KeyRange) ([]*copTask, error) {

		logutil.Logger(context.Background()).Debug("coprocessor encounters",
			zap.Stringer("lock", lockErr))
		ok, err1 := worker.store.lockResolver.ResolveLocks(bo, []*Lock{NewLock(lockErr)})
		if err1 != nil {
			return nil, errors.Trace(err1)
		}
		if !ok {
			if err := bo.Backoff(boTxnLockFast, errors.New(lockErr.String())); err != nil {
				return nil, errors.Trace(err)
			}
		}
}
```


