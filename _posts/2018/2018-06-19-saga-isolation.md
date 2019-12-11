---
layout: post
title: "2PC, Saga, and TCC" 
description: ""
category: 
tags: [interview, arch]
---

### 2PC
* 2PC assumes that data in stable storage is never lost. No node cashes forever
* Classic 2PC blocks when a machine fails - it locks the object down for further changes, until at the commit
* 

### TCC
* Try: reserve resources
* Confirm: execute without checking, idempotent. Note that txns other than the one from main service can be executed asyncly
* Cancel: cancel the execution, release resources done by the try. Idempotent. Note that txns other than the one from main service can be executed asyncly

To defend against isolation problems below
* need to have communtative updates between try and compensate, or re-read valueo
  *  mark the OOO try as rollback only - can not execute
* txn should have a globally unique id

### Lost update - commited txn change got overwritten

Scenario:

1. T1 creates order
2. T2 cancels order
3. T1 approves the order - without proper check, T2's change can be ignored!

### Dirty Read - Read uncommited changes

Scenario:

1. T1 reserves credits
2. T2 reserves credits, before parent txn of T1 commits


Note: 
1. In traditional DB, read committed isolation level can prevent it
2. In distributed, we can again use a semantics lock, although need to also introduce timeout +  random slacked, retry to prevent possible deadlock


### Non-repeatable read - read twice within the same txn. Got different results

Scenario:

1. T1 creates order
2. T2 updates order
3. T1 tries to finalize order , but find the state changes already!

