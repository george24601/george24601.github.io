---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

402C: Searching for Graph
------

Base case: when n = 5. K5 has 10 edges, i.e., p = 0

when n = 6,  K6 has 15 edges, i.e., p <= 3

idea : connect v6 to 2 + p existing nodes in n = 5

when taking a k element subgraph, total # edges = edges in K5 + |edges to v6| <= 2 (k-1) + 2 + p => we are good

when n = 7, K7 has 21 edges, i.e., p <= 7. So we can have A(6, 3), and then connect 2 + p - 3 edges to nodes in A6, if p > 3, if p < 3, we
just add 2 edges to 1 and 2, and construct A(6, p)

when n = 8, K8 has 28 edges, i.e., p <= 12, So we can have A(7, 7) , and then connect 2 + p -7 edges to nodes in A7

i.e. we find a way to construct answers

Note the official solution has different insights


339C: Xenia and Weights
------

good(round, weightUsed, balance) = 

```
LPE(lw, 1, 10)
  if(w < b && s[w] == '1' && s[lw] == '1')  
    good(r, w,b) = good(r, w, b) || good(r-1,lw, w - balance)
```
  
10^5 states, each cell iterates 10 times to update => 10^6 run time



287C: Guess Your Way Out
------
```
  1. by induction, if the leaf exists in left child, we do not have to leave that child. On the other hand, if the leaf does not exist, we
will have to visit all before

  2. base case h = 1

  3. ans(L, h, n) = ans(R,h - 1, n - 2^(h-1)) if exit in the left, or 2^h - 1 + ans(L, n)

  4. ans(R, h, n) = ans(L,h - 1, n/2) if exit in the right, or 2^h - 1 + ans(R, n - 2^(h-1))
```

150A: Win or Freeze
--------
Calcualte non-trival prime divisors of 2. Note that npd != 1, because mutliplication requires 2 numbers

```
  1. if npd = 0, A wins
  
  2. if npd = 2, B wins

  3. if npd > 2, A takes all but 2 prime divisors, and and hand over result to B

  4. Therefore, B wins iff npd = 2, we just need to write a quick check to see if npd = 2. So we start from 2, and find first divisor of d
<= sqrt(q), and check if q/d is prime or not

  5. Alternatively, we can iterate though [2, sqrt(q)], to calculate # of divisors. Note that if after the iteration, q is not 1 yet, we
need to add that 1 prime divisor to the total count!

```

This is a typical problem that can be broken into 2 parts, with each part solved differently and answer to the first part provides context
to the second part






