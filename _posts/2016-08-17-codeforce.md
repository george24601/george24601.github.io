---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

448D: Multiplication Table
--------
final answer <= 10 * 10^10, we can just bsearch the answer, at each guess, check form 1 to n, we can check how many > answer, and how many = answer

if nl + ne = k
  ne > 0 we are done. Otherwise, the guess is too small  
if nl + ne > k
  if nl <= k-1 we are done
  otherwise, guess is too small
if nl + ne < k
  our guess is too big


493C: Vasya and Basketball
---------
Sort a and b, we then increase d from 0 to max(a[i], b[i]), with each step a value of a or b , and update score as we go

Note that the two pointer approach does NOT work here! The reason is that the sliding window generally works when everything is triggered by
moving your end pointer, and front pointer merely follows. However, in this case, because we need to handle the bands of equal values on
both sides, when the front pointer moves, it has to update the state, thus violates the pattern.


  
590A: Median Smoothing
---------

For any a[i] = a[i+1], this segment will be stable

therefore, the seq can be partitioned by those continous bands

consider the case: 1010101..01 each round takes out a 0 

consider the case: 1010....0, each round moves the leftmost 0 to the right, until all 1s and 0s are together

so we iterate through the seq, and detect those alternating bands 

However, during implmentation, note that to detect alternating band, we should use a[i -1] != a[i] && a[i] != a[i+1], instead of just one
side. Otherwise, the code can not handle the case of crossing the boundary of continous bands


621C: Wet Shark and Flowers
---------
Two commmon tricks: reduce bands to prefixs, and caluclate complements.

Note that if we have to calculate Pr(A) + Pr(B) - Pr(A) * Pr(B). Often it is a sign we might as well calculate its complement!

```
  1. Total E = sum of Ex for each shark
  
  2. Pr(i generates a prime) = (# of primes <= R) - (# of primes <= L)

  3. Pr(product of i and i+ 1 is prime) = 1 -  (1 - Pr(i generates a prime)) * (1 - Pr(i+1 generates a prime))

```


