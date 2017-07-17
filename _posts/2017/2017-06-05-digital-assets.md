---
layout: post
title: "Digital Assets Platform"
description: ""
category: 
tags: [architecture]
---

Problems with existing solutions
---------
1. current way, no single source of truth, reconcliation at each participanets

2. Bitcoin - too specialized

3. Ethereum: access control: third part not involved can view all transactions and terms of those contracts

4. Obfuscating data: suspectable to post-facto analysi, unable to fultill regulations

5. logically segregated ledgers: can not solve forward secrecy problem


Architecture
----------
1. Business Logic Layer and Distributed Ledger layer. Application layer belongs to the customer

2. DL : Global Sync Log and Private Contract Store 

3. Each participant has its own PCS. PCS is stored locally, and does not process business logic => BL handles that.

4. GSL is a log of commitments and notifications => common and complete seet of valid transactions. o

DAML: not turing complete, but formal-method friendly. flexible enough to satify legal framework for financial agreements


Operations
----------
1. Reading: monitoring and receiving info from ledger and re-execute the logic that produced the events

2. Signing: authentication and authorizing transactions using private keys

3. Writing: committing evidences of data found in the PCS to the GSL. Only Operator role can do that. Use BFT Consensus Algorithm to handle the case of multiple operator


Sequence of actions
----------
1. Application layer issues DAML 

2. Operator persist contract in PCS, and insert hashed evidence to GSL

3. Evidence in GSL is broadcasted to all network participants

4. The participants affected are notified by the GSL

5. At the affected participant's BL layer, requests receipt of contracts from the Operator

6. Affected participant executes DAML of the contract, and persist the contract by that DAML into its own PCS

7. DAML event is sent to Participant's local application layer
