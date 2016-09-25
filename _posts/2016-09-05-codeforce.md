---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

451C: Predict Outcome of the Game
-------
Given we have the value of 2nd, we have 4 combos for the +, - of d1 and d2, therefore, we can calculate the sum of v1, v2, and v3. We just
need to ensure that each v is in [0, k] 


5C:  Longest Regular Bracket Sequence
----------
Maintain a stack , with each entry knows which index it is from. when we pop the entry, we update the length or the length count

However, know that the length will be from the SECOND top, because that the last time we do not have a regular string. Otherwise, the composition case ()() will fail!


416C: Booking System
----------
Consider the request with max p(i), and can fit into a table, we should accept it. Otherwise, we can improve the solution

Therefore, sort requests by p(i), and then get lower bound for each pi, if lower bound does not exist, reject


710C: Magic Odd Square
----------
At row i <= n/2, E * i , and then n - 2 * i O, and then E * i again

At row i = n/2 + 1, only Os

At row i > n/2 + 1, E *(n -i), then 2 * i - n, O and then E * (n - i again)

We can prove by induction this arrangement is correct


