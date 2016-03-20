---
layout: post
title: "Topcoder SRM: DoubleWeights"
description: ""
category: 
tags: [topcoder, dp, graph, multi-dimensional, complete-search]
---

Look at the Unknown: min(W1 * W2)

```
  1. After some trials, there appears no patterns between values of W1 * W2, i.e, I could not find the pattern that I can travel from the
min(W1 * W2) to a different point

  2. However, to build a path, we almost want to add edge one at a time, and 1) shows that no obvious pattern in W1* W2 after I add an edge
to an exisiting/partial path

  3. This suggests that W1 and W2 should be tracked independently, and combined only during the last step, i.e., we should not compute W1 *
W2 when we are building a path, one edge at a time.

```
  
By tracking W1 and W2 independently, we can swap part of the unknown with knowns and invent a new related subproblem:

```
What is the min value of W1, if we know the value of W2? Note W1 and W2 are from the same path.
```

Also note that each edge weight is between 1-9, # of nodes < 20, i.e., we can complete search on all possible W2 (< 200), and the min value
problem can be solved using a modified standard graph algorithm. In this case we will use Bellman-Ford, as we care paths from only 1 source node.

So here is the pattern/steps

```
Step: visiting from node nA to nB via one edge

State change:  minFirst(nA, W2) + First(nA, nB) = minFirst(nB, W2 + Second(nA, nB)) if it is less than current value

```

Inline with Bellman-Ford, we apply relaxation for each node in each round, and repeat such round for n times, since the path can not be
longer than n
