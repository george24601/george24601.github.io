---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

384C: Milking cows (!!!)
---------
This is type of the problem where we decompose by classification, and hopefully the target function is a simple composition of each class,
without each class overlapping of each other's cases
question is: how do we classify?

We can just focus on the cases of 2 cows, 4 cases
```
1. >< : we will lose 1 regarldess of which side we pick

2. >> : we should try pick the left most one first

3. <>: we will not lose any regardless of which side we pick

4. <<: we should try picking the right most one first

```

since case 1 and 3 gives constant cost, if we can just need to minimize case 2 and 4 AT THE SAME TIME, we can reach the minimal cost

Fortunatly, we can decompose the target string into >>>>> and <<<<< : even the two interleaves, how they interleave doesn't affect the final
target functions 

So we just need to calculate the total cost of case 1 => do a linear scan with state update and we are good


118D:  Caesars Legions
---------
Consider the compliement of the problem:  how many ways to have no more than k1 consecutive Fs or K2 consecutive Cs?

```
  ways(nF, nC, F) = ways(nF-1, nC, C) + ways(nF-2, nC, C) + ..... ways(nF-k1, nC, C)

  ways(nF, nC, C) = ways(nF, nC - 1, F) + ways(nF, nC-2,  F) +....ways(nF, nC - k2, F)

  ways(1, 0, F) = 1 and ways(0, 1, C) = 1

answer is 2^n - ways(nF, nC, F) - ways(nF, nC, C)

```

