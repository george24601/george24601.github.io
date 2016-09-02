---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

229A: Shifts
-------
precompute LC(i, j) = min number of left shifts to make t(i, j) = 1, and similarly RC(i,j)

To compute RC(i, j)

```
  last1 = index of right most 1

  LP(j, 0, m)
    
    if (a[i][j] = 1)

        cost = 0;
        last1 = j

    else
        cost = last1 > j ? m - last1 + j + 1 :  lasst1 - j - 1
```

To compute LC(i, j), is similar

after that BF on the final column that is all one = m * n 


484B: Maximum Value
---------

Notice that the value range is small, this and plus it is mod based means we can operate/iterate on value directly

we need to brute force on a(j) over all entreis, for each a(j), we look for prev(2*a(j)), prev(3*a(j)),... and update final result as we go

prev(a(j)) can be pre-computed. the total iteration cost is sum(2M/a(j)) <= O(MlogM). Note that this cost analysis is exactly same as
Sieves.

Onte that we need to sort entries, and ignore duplicate entires, otherwise, the sieve analysis may not work: we may be repeating low values
too many times

