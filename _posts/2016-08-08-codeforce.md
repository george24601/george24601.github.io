---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

543A: Writing Code
--------
Note that we are actually looking for a O(n^3) solution 

way[i][j][k] : 1...i to write j lines of code with total bug k

ways[i][j][k] = sum of (ways[i-1] [j - j1] [k - j1 * a(i)) => This gives O(n^4)

Alternativly, it is equal to

ways[i-1][j][k] + ways[i-1][j - 1] [k - a(i)] //i doesnt write a single + i writes at least one line

Notice this recursion is really similar to how you calculate binomial coefficents

Note that although O(n^3) run time is ok, but space is not ok. Therefore, we need to reuse the overwrite dp state trick, i.e., we use a 2-d
array, and on calculating each ways, ways[i-1][j][k] is actually the cell we are about to update, and ways[i][j-1][k-a] is the (j-1, k-a)
cell we updated in this i round, i.e., i should be the outmost loop



703C: Chris and Road
-------

Idea 1:
-------
The two moving against each other is equivalent of tourist moving in diagonal against static object. Therefore, he has to wait if that
diagonal cuts into polygon. If it does, then tourist has to wait until the line just meets a single point of the polytime. The amount of
time to wait is equal to v/(horizontal shift to make the diagonal tangent). 

Note we can use bsearch to brute force the shift


Idea 2:
-------
We hold at the origin, until polygon passes a certain point, and then we go full speed toeard to end

If we may crash into the bus, then the final time >=  (w - y)/u + x/v, for any point in the polygon.

Claim : the optimal way is the MAXIMAL value. 

Proof: we will get better overall time if and only if we start earlier given our schedule.  but it will hit the polygon, because we will
have a case where final time < (w -y)/u + x/v, directly violating our conditions


What I did wrong during the contest
----------

```
  1. upon realizing that the man has to wait and it is hard to describe it, I should transform the problem into something more approachable,e.g. , the tangent approach or
the hold-release approach

  2. the lastT < passTime check did not consider the case of points on the same y-axis, and therefore, need to introduce a max filter

```









