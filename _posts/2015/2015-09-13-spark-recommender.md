---
layout: post
title: "Spark recommender example" 
description: ""
category: 
tags: [spark]
---

Input:

  * User ID,  artist ID, and played count
  
  * artist to ID

  * artist aliases ID to canonical ID


Workflow,i.e., how to preprocess data:

1. Make sure IDs are within range with RDD.stats(), due to restriction of ALS implementaion in SparkML

2. Generate artist aliases ID to canonical ID map. This map is relatively small, so we can cache it in memory by broadcasting it

3. map artist ID in the main data set to the canonical ID. Note that default # of partitions = # of HDFS blocks, for computation intensive tasks,
probably want to re-partition the main data set to higher #.

