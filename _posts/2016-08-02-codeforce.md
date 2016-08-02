---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

350C: Bombs
-------
Standard greedy problem

Idea 1: group bombs by rows, sort each row by cols. 

Idea 2: If A is on the path to B, then we know mahatann distance of (A) < MD(B), i.e., if we process bombs in the order of MD, we know that all potential blocking bombs are cleared already


334C: secrets
-------

For the optimal solution, we need to use as many small denomincations as possible in the place of large ones,i.e., the optimal solution have
only the smallest denomination, because for any other solution, we can replace larger coins with smaller one and improve the result.

Consider any potential solution, the smallest denomination can not divide n, because otherwise, we can take out that denomination, and the
sum is either still greater than n, or < n, which implies the sum = m.

Therefore, in the optimal solution, the smallest coin should not divide n


525C: Ilya and Sticks
-------
Suppose we have 4 pairs a < b < c < d, c * d + a*b > a * d + b * c, i.e., we need to be greedy, and form the first pair we can

Idea1
-----
Saw as you go. Start scanning len from max to min, 

```
  1. push all evens to the result list
  
  2. if there is 1 remaining left, and count[len-1] > 0, push an additonal pair

  3. In the final run, iterate through pairs and output

```

Idea 2:
-----
Do all sawing first, and then look for the matching pairs. Scan len from max to min

```
  1. if count[len] is even, no need to saw

  2. if count[len] is odd and count[len-1] > 0, saw one. Note that since we saw at most one stick at each length and only when there already exists a stick with len - 1, we know that we are not re-sawing the same stick twice

  3. if count[len] is odd and count[len-1] == 0, count[len]--

  4. Now our scan handles only even entries, and we need to keep track of only the outstanding pair

```

272C: Dima and Staircase
---------
Notice that the first block always get covered for each drop. So we need to keep track of height of the first block, when there is a drop,
the answer will be max(right anchor, first block), and we need to update first block height afterwards

Note that without the covering first w(i) requirement, this needs to be solved with a segment tree
