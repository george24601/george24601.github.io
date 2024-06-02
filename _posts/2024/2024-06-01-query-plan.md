---
layout: post
title: "Notes on query plan optimization in RDBMS"
description: ""
category: 
tags: [paper, db]
--- 

* Steps in Query Processing: query -> parsing -> RA expressions -> optimizer with stats -> execution plan -> eval engine with data -> output
* Query optimizers use equivalence rules to systematically generate expressions equivalent to the given expression, i.e., heuristic optimization and cost-based optimization
* Frequently used approach: heuristic rewriting of nested block structure and aggregation. Followed by cost-based join-order optimization for each block
* Optimizers often use simple heuristics for very cheap queries, and perform exhaustive enumeration for more expensive queries 
* Push down projection also reduces number of pages since it eliminates columns early
* Heuristics 
 * Consider only left-deep plans
 * Avoid Cartesian products
 * Don’t optimize the entire query at once. Break query into query blocks and optimize one block at a time
   * A query block contains a single SELECT-FROM-WHERE expression as well as GROUP BY, HAVING clauses
 


### References

* [Chapter 12: Query Processing](https://www.cbcb.umd.edu/confcour/Spring2014/CMSC424/Query_processing.pdf)
* [Chapter 13: Query Optimization](https://www.cbcb.umd.edu/confcour/Spring2014/CMSC424/query_optimization.pdf)
* [Query Optimization](https://cs186berkeley.net/fa20/resources/static/notes/n09-QueryOpt.pdf)
* [Relational Query Optimization I: The Plan Space](https://drive.google.com/file/d/1EDL712gC8NDO1iRI3_EjtltiQOwpitmX/view)
* [Query Optimization](https://web.wlu.ca/science/physcomp/ikotsireas/CP465/W2-QueryOptimization/Query_Optimization.pdf)

