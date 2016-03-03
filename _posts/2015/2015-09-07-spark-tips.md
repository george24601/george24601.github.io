---
layout: post
title: "Spark Tips from Learning Spark" 
description: ""
category: 
tags: [spark]
---

1. Use persist() if an RDD may be triggered in many acitons. You need to persist() before the first action.
2. Any ops that removes duplicate will be expensive due to shuffling across network. Note that substract() shuffles as well.
3. collect() can be used in unit test, since most likely data will fit in memory
4. Use rdd.partitions().size() to decide if you can coalesce()
5. RDD is immutable, so need to persist() at the correct place.
6. To keep partitioner for k-v rdd, use mapValue() and flatMapValues(), because map() may change key
7. If we skip ill-formed data, use accumulator to report errors
8. Do not include spark itself in submitted dependency
9. SparkConf is immutable.
10. Kyro serialization - Spark by default does NOT use it
