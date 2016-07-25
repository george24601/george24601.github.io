---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

334 A: Alternative Thinking
----------
Flipping a substring can be decomposed into 2 prefix flips. Easy to prove each flip increase the score by at most 1, and if there is an
optimal solution (i.e., can increase), there is one that flips at the first 00 or 11. Therefore, we just look for total number of 00s and
11s, cap it at 2

112 C: Another Problem on Strings
----------
Similar to the problem above, substring can be decomposed into delta of 2 prefixs. This means substring with k 1s = prefix with n 1s and a smaller preifx with n-k 1s

Therefore, num of substrings with k 1s = sum of ( # of prefixs with n 1s *  # of prefixes with  n-k 1s)
where n is from k to n

211 C: Fixing Typos
-------
Easy problem, but can you solve it with one pass only. 

Consider the inductive steps: if my current string is valid, and I try appending new one, what should I do to ensure it keeps valid?

9C: Hexadecimal Numbers
----------
Need to know how many numbers < n that has only digit 0 and 1?

Idea 1: recursive computation
```
  num(n) =
    if (n = 1) return 2

  if top digit is 1
    num(n - 10s) + num(10s - 1)

  else if top digit is not 1
    num(n - remainig + 10*)
```

Idea 2: Generation-Filtering
Generate all numbers with digit 0 and 1 that can possibly be less than < n, i.e., 9 digits, then filter out those >= n. This works, because
we generate < 2^10 = 1024 numbers
