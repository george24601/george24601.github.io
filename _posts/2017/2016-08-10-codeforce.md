---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

540C: Ice Cave 
--------
If there is a solution, we know that

```
  1. there a path from (r0,c0) to (r1, c1) without touching existing Xs

  2. (r1, c1) must have at least 2 . neighbors if it is . itself, one for the incoming edge, one for stepping out and back

  3. note that we do not need more than 2, because we can always reorganize our paths to use only 1 . neighbor, if there exists any path

```

Special case is r0 = r1 and c0 = c1, where you just need one . neighbor


463C: Gargari and Bishops
--------

There are only 2 classes of diagonals: 
1. captureable from (1,1), by only going diagonals

2. captureable from (1,2), by only going diagonals

proof: induction on the size of n, base case n = 2 and 3, each time n -= 2

Therefore, we go through each point, calculate which class it is from sum of the value it captures, and update that class maximum

A point is in class 1 in if abs(y -x) is even, otherwise when abs(y-x) is odd, it is class 2

pre-calculate all 2 * n diagonal sums for quick retrival. For all left leaning diagonals, cells with same i-j are on the same diagonal. For the right leaning, cells with i+j are on the same diagonal


552C: Vanya and Scales
--------
Consider the number in w-nary representation, from the smallest digit
form.

if it is 0, do nothing

if it is 1, we put our only weight on the RHS

if it is w - 1, we put our only weight on the LHS, 

if it is other digit, we can do nothing

Note that we go small > large instead of the other direction is because we are doing additions and advancing a digit will affect downstream ops


329A: Purification
---------
Consider with out E

Claim: we need at least n spells

Proof: induction on n, n =1 obvious
n = i, if it can be done with < i spells, we can move those points to within the i-1 range, thus contradicts the inductive hypothesis

If we have n spells, how should we cast?

if we can find a X or T of Es, then we know there is no solution, otherwise, one must exist, because we can fill into that x or T hole and remove those Ts

if there is a row of E, consider the final solution, we know we should cast spell on each column once, and conversely such will satisfy a solution, since and X or T do not exist, we know that each column has a cell we can fill, we just fill it. After n rounds, all n columns are filled.

if there is a col of E, then we go after each rows with steps similar
