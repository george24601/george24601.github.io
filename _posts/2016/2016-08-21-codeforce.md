---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

246D: Colorful Graph
---------

For each edge, consider C(u) and C(v), we just update Q(c(u)) and Q(c(v)), and in the end select the set with max cardinality

similarly, it is as if we create a graph based on colors, and add edge between colors


459C: Pashmak and Buses
--------
This one uses the trick of focusing on individual in collective actions

Consider the unknown: if the two students have the same assignment each day, what would that suggest?

Consider the sequece of assignment for each student, there are k^d possible choices, this means the two students will be friends if and only
if they share exactly a sequence of assignments. Therefore, if n > k^d, we know it is bad. Otherwise, we can assign a unique sequence id to
each student

My wrong approach focused solely on collective actions, which gives a maximum of k * d


C. Bear and Prime Numbers
----------

My (Failed) Idea
-------

```
  1. generate all primes <= 10^7

  2. factor all n numbers, for each factor, increase the match[factor] by 1

  3. sweept through the array generated by 2, to compute the prefix sum

  4. the range becomes difference of prefix sums

```

The basic idea is sound, however, the main problem is that factoring in step 2) is too slow. Instead, we can use the sieve to reverse the
factoring process, and calculate answers for all numbers in a single runr, i.e., instead of increasing match[factor] by 1, we can increase
it by f(p)

429B: Working out
-------------
Consider the cell they meet at (mi, mj), one has to enter horizontally, and the other has to enter vertically, otherwise, they will have
more than 1 same cells, we just need to iterate all possible meeting points, each with 2 possiblites

also, note that the path is effective cut in 2, and each half must exist, i.e., meeting point can not touch border!

so 2 * 10^6 to calculate the cost in 4 directions + 10^6 brute force on meeting points


311A: The Closest Pair
--------

the total number of ops <= n (n-1) / 2, therefore, if k >= that max # of ops, no answer

Notice that we only require delta(x) < d, then we can force all points to have to same x coordinate, and then put them on the same y-axis, the distance between them does not even matter