---
layout: post
title: "Understanding Kafka exactly once semantics" 
description: ""
category: 
tags: [kafka]
---
The whole process involves many moving parts. I will approach it by viewing the process from each components POV.

### Producer

* Producer will register its transaction id/PID with the transactional coordinator (TC), and get a new epoch from TC, with sequence number = 0 inside producer's memory

* Producer start producing to partitions. 
	
	* To broker, each message will include PID, epoch, and sequence number. After sending a batch to a parition, producer will increase its own seq # by 1.

	* To TC, producer will register the (topic, partition, PID, epoch). Note if we use Stream API, the consumer offset  will be written too.

* When producer commits, it will ask TC to write a PREPARE_COMMIT(PID, epoch) message internally. 


### Consumer

* Assume the we set consumer isolation level at read committed

* consumer will filter out messages from aborted transactions, and will block on the first message from ongoing transactions

* Consumer will get a list of aborted PIDs whose offset range intersects with the fetch batch size from the consumer coordinator. This means filtering is done on consumer's client side

### Broker

* The broker process also hosts the coordinator. Thus, the HA of 2PC coordinator is guaranteed via Kafka's partition leader election.

### Trasaction Coordinator

* Transaction log just stores the status of transactions instead of actual message it self

	* Ongoing (PID, epoch) - analogous to the PREPARE-stage of 2PC

	* (PID, epoch, topic, partition) - information to commit for PRODUER

	* (PID, epoch, topic, partition, offset) - information to commit for CONSUMER

	* Prepare-commit(PID, epoch) - marks the PREPARE-stage of 2PC done. This means we will always

	* COMPLETED(PID, epoch) - end of log marker for 2PC


### Consumer Coordinator

* manages the internal consumer offsets topics
