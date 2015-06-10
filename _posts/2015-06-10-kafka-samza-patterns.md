---
layout: post
title: "Samza-Kafka Design Patterns" 
description: ""
category: 
tags: [kafka, samza]
---
###Log holds the ultimate truth###
	We need to refer to logs for the final answer. This means streaming job should accept input only from the log, and not read from any other sources,e.g., file system or db. 

	All state changes that a samza job needs should be read from/write to its local state 

	Conversely, log should have all state changes required by the log consumer. This often means log holds ALL state changes 
	

###Samza local state and fault tolerance###
	In-memory varaibles should be viewed as optimization similar to caching,i.e., removing them shouldnt affect correctness 

	At-least delivery means you need to performance action first, and then persist the state change in the local storage.
	
###Samza local state k-v design###
	K-v design should not be based on the principle of (de)normalization. The expected query pattern should dictate design,i.e., instead of model  -> query, we have to do query -> model.

	Denormalization: expensive update operation, because you could read a collection, do only minor changes, and write the whole thing back to local storage
	
	Normalization: in-memory joins


###Log often is not presentable/consumerable###
	Therefore, we need to put them into another data sinks. Common choices:

	HDFS for immutable data

	HBase/Cassandra to receive writes from streaming jobs, and reads from API implementation

	ElasticSearch for search functions

	Note that role of data sink is for scalable read. The sink itself should not perform much, if any, analytical work. That is the responsiblilty of data sink consumer

###Counter design###
	Option 1: generate vote object with unique id, store unique ids, and  map/reduce to count the final number

	Option 2: generate vote object with unique id. Pipe them into HyperLogLog algorithm. A trade off between precision and space
"
