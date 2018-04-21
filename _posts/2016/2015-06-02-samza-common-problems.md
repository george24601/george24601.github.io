---
layout: post
title: "Samza Job Patterns"
description: ""
category: 
tags: [samza]
---
Overall, remember that each samza container runs only in one thread. Therefore, performance is very sensitive to your logic

1. Do not use MessageCollector outside process()

2. Try to limit local storage I/O. Our performance test shows RockDB, although fast, is CPU heavy

3. Try not to include cross process/network communication. If you have to, try to batch it to amortize cost. Do not println.

4. Take at-least-once delivery of messages into account. At-least-once semantics means you should apply the operation, update state, and then persist it

8. K-v design should not be based on the principle of (de)normalization. The expected query pattern should dictate design,i.e., instead of model  -> query, we have to do query -> model.  Denormalization: expensive update operation, because you could read a collection, do only minor changes, and write the whole thing back to local storage.  Normalization: in-memory joins

9. Log often is not presentable/consumerable.  Note that role of data sink is for scalable read. The sink itself should not perform much, if any, analytical work. That is the responsiblilty of data sink consumer.  Common choices:

	a. HDFS for immutable data

	b. HBase/Cassandra to receive writes from streaming jobs, and reads from API implementation

	c. ElasticSearch for search functions


10. Counter design
	Option 1: generate vote object with unique id, store unique ids, and  map/reduce to count the final number

	Option 2: generate vote object with unique id. Pipe them into HyperLogLog algorithm. A trade off between precision and space

11. If you need to mini batch processes for performance gains, then it should not be a samza job,i.e., your program should just read from/write to kafka directly(although you may still need yarn for resource allocation and restart). Concretely, samza job is used only when both your input and output are kafka topics. Import into kafka/export into data sink may even be a batch process such as mapreduce
