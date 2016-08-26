---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

371C:  Hamburgers
---------
Just bsearch the answers, and calcuate number of pieces one has to buy


471C: MUH and House of Cards
------- 
If we can form a higher level of tower, we can form a lower level of tower, i.e, we just need to look for the max level of towers

Given a tower of level h, we can know the min # of cards we need to build it. Therefore, we can just bsearch for the answer: find the
highest h s.t., f(h) <= n

f(h) = (1 + h) * h /2 * 3 - h


185A: Plant
--------
f(up, n) = 3 * f(up, n -1) + f(down, n -1)

f(down, n) = 3 * f(down, n-1) + f(up, n-1)

Eq 1 - Eq 2 and we discover a geometric series, and then we can calculate the power in O(logN)


623A: Graph and String
---------
s(i) = b <=>  deg(i) = n - 1

Note that if we have a solution, we can swap all as and cs and the result is still a valid result

s(i) = a <=> deg(i) = num of cs  + (num of as - 1). Therefore, all other notes not b will be a

The remaining will be c, and then we scan through all edges again calculate degrees for each v.


4D: Mysterious Present
---------
sort by (w,h), then O(n^2) DP, finally scan through answers that can fit the envelope


580D: Kefa and Dishes
--------
At first I tried brute force generate all set, and potentially find all optimal matching in the set, but I can not find an easy algorithm
to do so.

However, considering that a lot of graph-ish problem are very dp-like, we can locate the following relationship

score(set ending at i) = min(set ending at j, and i not in set) + score(i followed by j) 

2^n * n states, each state takes n to update



417C: Football
-----------
|E| = k * n, therefore , k <= (n-1)/2

when k = 1, total |E| = |n|, with each edge has an outgoing edge , i.e., a cycle 

when n = 1 or 2, impossible. 

when k is within the upper bound, we can just add edge the lexo sorted next k nodes
