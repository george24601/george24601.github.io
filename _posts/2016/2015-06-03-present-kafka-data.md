---
layout: post
title: "Discussion: Data Sink for Kafka" 
description: ""
category: 
tags: [kafka, hbase, cassandra]
---
###Our problem###
	Present kafka data to end user

	Read heavy, need to support simple filtering and sorting,i.e., closer to OLTP than OLAP, because our streaming process will handle the complex query part.

	Read may need to operate on 100m+ entries by 10k+ users. Latency < 1s
	

###How similar problem was solved ###
	Cassandra: Twitter (Cassandra for "sub-second API query" and "real-time querying"), Datadog

	ElasticSearch: Netflix, Loggy, Datadog

	Druid: Netflix, Metamarkets

	HBase: Tumblr, Mozilla, Cerner, DataSift....

	Hive: pinerest

	Most popular choices, in order, are HBase, ElasticSearch, Cassandra, and Druid.
	

###HBase vs Cassandra###
	Range reads: HBase
	Random reads: Cassandra
	Random writes: Cassandra
	Hadoop integration: HBase
	Setup and maintenance: Cassandra
	Latency: HBase
	Searching column Data: HBase
	Dynamic query on columns: HBase
	Access control: HBase
	Aggregation/roll up/across-row analysis: Cassandra
	
	

