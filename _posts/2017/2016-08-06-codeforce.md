---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

534C: Polycarpus Dice
---------

```
  1. max value can be reached  = min(d(i), A - (n - 1))

  2. min value can be reached = max(1, A - (sum - d(i)))
```


469C: 24 Game
--------

```
  1. n = 4, just multiple them together

  2. n = 5, possible the form a solution

  3. for all n >= 6, we can reduce to case n < 6, just keep multiply with i - (i -1), where i >= 6  

```

236C: LCM Challenge
--------

```
  1. obviously, there is an optimal solution where the any of the gcd(i,j) should be 1, so that the answer is a * b * c. Otherwise, we can reduce a solution into a format with this pattern

  2. if n <= 2, just pick n

  3. we know gcd(n,n-1, n-2) = 1, and n * n-1 * n-2 is maximal, if n is odd

  4. But if n is even, then we should pick n-1 * n-2 * n-3 

```

318C: Perfect Pair
---------

```
  1. if any number is negative, then we can not make from an non-perfect to perfect

  2. if any is posivite, just brute force it => if x <= y, turn into (x+y, y), we know the lower end increases by at least double 

```

578A: A Problem about Polyline
---------

```
  1. since polyline can not be higher than x, then we know b <= x

  2. if a < b, that is impossible to get the line

  3. if the point is on the rise,  a - b will be on the axis, i.e, (a-b) = 2k * x

  4. if the point is on the fall, a + b will on the axis a+b = (2k + 1) * x

  5. try both and take minimum

```

