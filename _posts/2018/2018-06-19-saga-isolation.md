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
* One we pass the prepare stage, we know the exeuction result will be all commited, or all canceled (when prepare is successful, but some participants failed to commit)

### TCC

* Try: reserve resources
* Confirm: execute without checking, idempotent. Note that txns other than the one from main service can be executed asyncly
* Cancel: cancel the execution, release resources done by the try. Idempotent. Note that txns other than the one from main service can be executed asyncly

### Saga

#### P2p distributed saga

* The Order Service creates an Order in a PENDING state and publishes an OrderCreated event to Cutomser Service
* The Customer Service receives the event attempts to reserve credit for that Order. It publishes either a Credit Reserved event or a CreditLimitExceeded event to Order Service
* The Order Service receives the event and changes the state of the order to either approved or cancelled

### Centralized coordinator saga

1. The Order Service creates an Order in a pending state and creates a CreateOrderSaga
2. The CreateOrderSaga sends a ReserveCredit command to the Customer Service
3. The Customer Service attempts to reserve credit for that Order and sends back a reply
4. The CreateOrderSaga receives the reply and sends either an ApproveOrder or RejectOrder command to the Order Service
5. The Order Service changes the state of the order to either approved or cancelled
7. Note that even in centralized case, steps are performed IN ORDER instead of parallel

### Saga's isolation problem

#### Lost update - commited txn change got overwritten

1. T1 creates order
2. T2 cancels order
3. T1 approves the order - without proper check, T2's change can be ignored! That is, need domain logic to handle when when we are approving the order that is already cancelled

Similarly, during compesntation, the previous result of do is no longer there because of another txn. 

#### Dirty Read - Read uncommited changes

1. T1 reserves credits
2. T2 reserves credits, before parent txn of T1 commits

#### Non-repeatable read - read twice within the same txn. Got different results

Scenario:

1. T1 creates order
2. T2 updates order
3. T1 tries to finalize order , but find the state changes already!

* To defend against isolation problems above
 * need to have communtative updates between try and compensate, or re-read value
  * mark the OOO try as rollback only - can not execute
  * txn should have a globally unique id
* In traditional DB, read committed isolation level can prevent it
* In distributed, we can again use a semantics lock, although need to also introduce timeout + random slacked retry to prevent possible deadlock
* Domain logic should get money first before giving money to reduce the loss if compenstation failed due to concurrency

### TCC vs saga

1. try vs direct
2. in sequence vs parallel
3. compenstation order
4. For user POV, TCC offers better consistency, because try stage will reduce the likelihood of user seeing partial results. However, partial results cases can always constructed due to the nature of eventual consistency
