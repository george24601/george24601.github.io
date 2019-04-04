---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

456C: boredom
-----
Consider each number appears only once

If we have 3 consecutive numbers, we should take 1st and 3rd

If we have 4 consecutive nubmers, we shoud take 2nd and 4th

i.e., if there no repeats, start the biggest, and we take every other numbers

If there are repeats,

best(n) = max(score(n) + best(n-2), score(n-1))

so sort the number, then

best(n) = max of 

```
  1. score(n) + best(n-1), if a[n] > a[n-1] + 1

  2. score(n) + best(n-2), if a[n] = a[n-1] + 1 

  3. best(n-1), if a[n] = a[n-1] + 1
      
```

269A: Magical Boxes
---------
Sort (k, c) pairs

start from 1 to n-1, calculate, how many box of this size can fit all previous sizes. Set balance to max(boxes to fit all, a[i])

if balance =1, we are done. Otherwise, the final answer is kmax + log(4, balance) + 1


204A: Little Elephant and Interval
--------
Standard transformation: calculate all such pair < R

for numbers with m digits , all possible number is 9* 10^(m-2)

For numbers with same number digits as R, 

if first digit  <= last digit, (fd - 1) * 10 ^(m-2) + middleDigits + 1

if first digit >= last digit, same answer, (fd -1) * 10 ^(m-2) + middleDigits


437C: The Child and Toy
----------

What will an optimal solution look like?

Consider the vertex with biggest value, since all edges must be brokean, we want they to be borken from the higher end, i.e., the highest
value vertex should be removed first, to satisfy the conditions.

Repeating this process, we can form a sequence of actions, where for each edge, we guarantee that the edge is broken from the bigger end,
i.e., the smallest possible cost is incurred
