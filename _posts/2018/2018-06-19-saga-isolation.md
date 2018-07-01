---
layout: post
title: "Isolation Problems for SAGA/TCC" 
description: ""
category: 
tags: [pm]
---

Lost update
---------

Symptom: commited txn change got overwritten

Scenario:

1. T1 creates order

2. T2 cancels order

3. T1 approves the order - without proper check, T2's change can be ignored!

Note:

1. Such thing will not happen even in the laxest single instance DB

2. To defend against, we need to have communtative updates between try and compensate, or re-read value


Dirty Read
--------

Symptom: Read uncommited changes

Scenario:

1. T1 reserves credits

2. T2 reserves credits, before parent txn of T1 commits


Note: 

1. In traditional DB, read committed isolation level can prevent it

2. In distributed, we can again use a semantics lock, although need to also introduce timeout +  random slacked, retry to prevent possible deadlock


Non-repeatable read
---------

Symptom: read twice within the same txn. Got different results

Scenario:

1. T1 creates order

2. T2 updates order

3. T1 tries to finalize order , but find the state changes already!


Other countermeasures
---------
1. version file

2. by value, semantic ACID

3. ideally, compensataing requests should be commutative 
