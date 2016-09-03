---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

448C: Painting Fence
---------
Notice that, if we use a H, either it touches top of fence, or it has followup Hs to touch the top of fences

This means that, if the lowest fences are not painted by H, all are done by V

Consider the last fence, is it painted by H or V

if by H, then it is same as with n-1 entries, with base height 0

if by V, then it is same as with n-1 enights, with base height h(n), 

ans(i, baseHeight) = 

```

if(baseHeight >= h(i))

  ans(n-1, h(i))  

else min of

  ans(n-1, baseHeight) + 1 //vertical

else
  ans(n-1, h(i)) + h(i) - baseHeight //horizontal


```


234C: Weather
--------
Scan through array, calculate prefix sum of number of positive, negative and 0, entries

scan through array again from 1...n-1, assume the index as the first positive number, and then calculate the potential cost



598D: Igor In the Museum
---------
Flood fill problem: DFS for each component, keep track of the cell belongs to which component, and how many paints this component can visit

Upon seeing query, just retrieve pre-computed result


321A: Ciel and Robot
--------
consider the 1d case, and only 1 dimensional: then either it reaches that in the first run, or never if the overall delta is in the opposite
direciton

If we can reach within 1 iteration => manhattan distance <= |s|

therefore, we try to move it so that its manhatann distance can be within |s|, after that we need to try 2|s| times traversing so that we
can make sure all within bound spots are checked


592C: The Big Race
--------
if w = b, always tie

if w != b, tie <=> t is [lcm(w, b), lcm(w, b) + min(w, b)], note that lcm(w, b) start with 0

total = (t - 1)/lcm(w,b) * min(w,b) + min((t -1) % lcm(w,b), min(w, b))


353C: Find Maximum
--------
just calculate f((prefix - 1) << i + 1s), iterate through all is



