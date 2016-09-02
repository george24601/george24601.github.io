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

```
LPE(intermediate, 2, n)

  LPE(from,  1, intermediate)

    LPE(to, 1, intermediate)

      best[from][to][intermediate] = min of(best[from][to][intermediate -1], e[from][to],
best[from][inter][inter - 1] + best[inter][to][inter-1])

      sumup delta in this round


  update to total sum here, add it to the output
  
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


