---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

159D:  Palindrome pairs
-----------

Idea 1
------
For each i, we can calculate longest palindrome formed with i as center, while we are expanding, we can add that to RE[i+len], and LE[i -
len], i.e., number of palidromes that with i+len as right end, and i-len as left end, and then we iterate all RE[i] * LE[j] with i < j

Idea 2
-----
Suppose np[i], number of palidromes within 1...i, and then we calculate p[i][j], i.e., is the substring i..j a palindrome. so np[j] =
np[j-1] + p[k][j] k over all 0..j

Final answer is np[i] * (np[n] - np[i]) over all i



219C: Color Stripe(!!!!)
---------
k = 2, just need to brute force one of the two possible answers

k > 2, just go though each letter and change every same neighbors, just need to make sure it is different from both sides


225C: Barcode
--------
Consider the base case n = 1

ans[i][true] = cost to make a solution, where column i is white

is min of 

ans[i -1] [true] + whiteCost(i),  ans[i-1][false] + whiteCost(i)

ans[i] [falst] = cost to make a solution, where i is the last black band of the stripe

ans[i-x][false] + blackCost(i-x + 1, i) ....ans[i-y][true] + blackCost[i- y][false]

final answer is min of ans[n][true], ans[n][false]

