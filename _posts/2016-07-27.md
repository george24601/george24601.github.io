---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

207-A: Knight Tournament
-------
Easy problem with c++ set and it O(logn) upper_bound. But it can be solved with the parent[x] array, plus the path compression trick in
union-find data structure

265-A: no to palindromes
-------
Suppose we know the first index to flip
the first letter is not same and prev and prev - 1, then we can keep generating, with this rule from a, knowing that it will not introduce
new palidromes. Otherwise, the inner part would have a palindromes already

One thing to notice is that since we are looking for the next one, we should try from the tail, so that we can break immedately upon finding
one

336-A: Chain Reaction
----------

The first idea is to DP by index i, i.e., ans[i] = 1 + ans[j] where j is larget num s.t. a[i] - a[j] < b[i], but this requires a bsearch

Can we avoid the bsearch?

Consider the value is within 1 mil range, at distance d

```
  ans[d] = ans[d-1] if d is has no lamp

  and[d] = ans[d - b[l[d]]i - 1] + 1 if d has lamp

```
