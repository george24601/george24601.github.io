---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

159D:  Palindrome pairs
-----------
For each i, we can calculate longest palindrome formed with i as center, while we are expanding, we can add that to RE[i+len], and LE[i -
len], i.e., number of palidromes that with i+len as right end, and i-len as left end, and then we iterate all RE[i] * LE[j] with i < j


219C: Color Stripe
---------
standard DP: best[j][c] = best[j-1] [c1] , c1 in all colors and c1 != c,
this gives 2 * 10^5 * 512 = 10^7, this is barely enough!

if c[n] = c[n-1], we for any color combination of  or c[1] and c[3]. we can find a solution

i.e best(n) = best(n-2) + 1

if c[n] != c[n-1], then best(n) = best(n-1), because consider the best solution with row ending at n-1 if c[n-1] is not painted, we can good, otherwise, we can pick a color so that c[n-2]
and c[n] are not same to c[n-1] anyway


225C: Barcode
--------
Consider the base case n = 1

ans[i][true] = cost to make a solution, where column i is white

is min of 

ans[i -1] [true] + whiteCost(i),  ans[i-1][false] + whiteCost(i)

ans[i] [falst] = cost to make a solution, where i is the last black band of the stripe

ans[i-x][false] + blackCost(i-x + 1, i) ....ans[i-y][true] + blackCost[i- y][false]

final answer is min of ans[n][true], ans[n][false]

