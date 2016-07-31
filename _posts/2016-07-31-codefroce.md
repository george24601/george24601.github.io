---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

384C: Milking cows
---------

suppose we have the optimital solution, what should it look like?

consider we have 2 cows at i and j with i < j 

```

  1. if a[i] = 0 and a[j] = 0, in an optimal solution, j is milked before i, and no milk is lost

  2. if a[i] = 0 and a[j] = 1, order does not matter in an optimal solution, and no milk is lost

  3. if a[i] = 1 and a[j] = 0, order does not matter, we will always waste 1 unit of milk

  4. if a[i] = 1 and a[j] = 1, in an opitmal solution, j is milked after i, and no milk is lost

```

Use the standard horizontal-vertical perspective switch. Instead of what happens during milking a single cow, consider i in the whole
milking process: each (i, j) combination will be evaluated at least once, therefore, the optimal cost >= number of pairs
of (a[i] = 1 and a[j] = 0), where i < j.

The question becomes, can the lower bound be really reached?  

```

  For this lower bound to be reached, this means all observeration must be respected!

  all i where a[i] = 0 must be orderderd according to 1)

  all i where a[i] = 1 must be ordered according to 4)

  each group forms a partial order. Note that the two parital orders can interleave due to 2) and 3)

  to construct a solution, we can look for speical case where the two parital orders do not interleave   
 
```
  
In the end, we need to calculate pairs with condition 3), we can do that with a linear scan




89 D:  Caesars Legions
---------
Consider the compliement of the problem:  how many ways to have no more than k1 consecutive Fs or K2 consecutive Cs?

```
  ways(nF, nC, F) = ways(nF-1, nC, C) + ways(nF-2, nC, C) + ..... ways(nF-k1, nC, C)

  ways(nF, nC, C) = ways(nF, nC - 1, F) + ways(nF, nC-2,  F) +....ways(nF, nC - k2, F)

  ways(1, 0, F) = 1 and ways(0, 1, C) = 1

answer is 2^n - ways(nF, nC, F) - ways(nF, nC, C)

```

