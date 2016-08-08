---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

354A: Vasya and Robot
---------
consider the final solution, 1...m will be picked by left, m + 1...n by right. 

Therefore, we need to brute force on the m. To minimize cost, we need to alternative L and R as much as we can.

if m <=  n/2,
  total cost = staticLeft(1...m) + staticRight(m+1....n) + (n - m - m -1) * rc
else
  total cost = staticLeft(1...m) + staticRight(m+1....n) + (m - (n -m) - 1) * rc


22D:  Segments
----------
Consider the left most seg, just need to be greedy to nail at its right end, and then take out all segs starting to the right of that end


282C:  XOR and OR
--------

Idea 1
-------
If they have different lengths, then of course we can not transform

Difference between XOR and OR: only difference is at 11, one is 0, the other one is 1

len = 1, can do only if they are same already

when len = 2

```
  1. 00 <=> 00

  2. 01 <=> 11 <=> 10
```

Claim: we can transform => we can start fixing from the tail (if side is obvious). Notice that each step is reversible, so we can force direction from a to b

proof: n = 1 or 2, obvious 

for case n, if 0-0 or 1-1 case, then we can follow inductive hypothesis, with nop at the end

if 0-1 case, the only way to transform, if possible, is to use rule 2,i.e, change the tail to 10, for all possible ways.Therefore, we can fix the order of application 


Idea 2
--------
Claim: any non-zero string can be converted to each other

Proof: any string can be converted to 10...0. From right to left, just keep applying 01 => 10, 11 => 10 


493D: Vasya and Chess
-------

n = 2, w wins immediatley

n = 3, all will go down until reach the bottom, and then white got captured by black

n= 4, white goes down, not sure how that work...

but if white goes right, then from the black perspective it is same as n = 3 case, where both have to reach the bottom first. i.e., black will lose for sure!

n = 5, if white goes left, black should go right, so that we have similar case to n = 3, i.e. black will win

if white goes down, black will go down as well, since now it appears that, the width matters most instead of heights, and this reduces to
the first case of n = 5

i.e., when n is even, we can always devise a strategy for white to win, when n is odd, we can always design 1 strategy for black to win



