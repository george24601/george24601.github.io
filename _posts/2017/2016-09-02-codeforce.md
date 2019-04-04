---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

295B: Greg and Graph
----------
Speical case: assume the removal is in the order of n...1

this is basically the reverse of floyd washall

Note that

```
  1. a brute force O(n^3) space will exceed memory limit. Therefore, we need to reuse the previous state. Note that this does not affect the
correctness, because len[from][inter][inter-1] = len[from][inter][inter] 

  2. Even if we care only the partial graph, we still need to build all nodes as we expand the inters, so that when the new inter node is
introduced, we can immediately connect with existing nodes. Also, because we only calc path passing inter nodes, we know we are not
calculating other SPs by accident

```


340D: Bubble Sort Graph
---------
for any (a[i], a[j]) tuple in the original graph  where a[i] > a[j] and i < j, we will add an edge to the graph, otherwise, the sorting
routine will not stop

Look at the unknown, we know that conversely, the 2 Vs inside the MIS does not have edge <=> they must map to 2 a[i], a[j]s where i < j and
a[i] < a[j]

Therefore, the answer is the longest increasing subsequence of the input permutation


182D:  Common Divisors
-----------
Iterate through all distinct prime factors of gcd(len(s1), len(s2)), check if the prefix of length of that prime factor is a building
block for both s1 and s2
Take into account it may have same prime factors many times


