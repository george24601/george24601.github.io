---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

645B: Mischievous Mess Makers
---------
Worse case: order from n to 1

Claim: in a optimal solution, regardless of k, n should be the first, and 1 should be last 

Proof: by induciton on k, when k = 1, obviously

When k = n, we apply the 1-n swap first, and then apply the induction hypotheis on (n-1, k-1) case

total bad introduced = (n- 1 + n - k) * k. Of cource we need to handle the case of k > n/2


166C: Median
-----------
sort the array, if the number does not exist, then we need to insert it first and then resort

We need to calculate the band range of the target number. If left is to the right of median position, we can reverse-calculate the length of
the target array, so that left becomes median = Left index * 2 - 1

Calculate similar if right is to the left of median

Otherwise, we are done already


476D: Dreamoon and Sets
----------
Suppose k = 1, we just use (1, 2, 3, 5) (4, 7, 9 , 11)(8, 13, 15, 17),

Note that if k > 1,  we can just map k=1 solution by multiplying k to each entry in both directions, i.e., we just need to solve k = 1 case!

so each group is the form of 1 even + 3 odds, we also notice that 3 consecutive odds are coprime for sure, because their difference is 2 or
4, which means their gcd can only be 1 or 2, but 2 suggests that they are odd, contradiciton

(!!!)
The final form is {6a +2, 6a+1, 6a+3, 6a+5},each to prove that 6a+ 2 is coprime with everyone else


364A: Matrix
----------
interate all (i, j) combinations to calculate count(x)= number of ways to form x, x >= 0 and x <= 4000 * 9

Then check all divisors of a up to sqrt(a), add count(div1) * count(div2) to the answer

the final answer needs to * 2, except when a has a perfect square


689C: Mike and Chocolate Thieves
----------
Make sure that m % 4! = 0, because all 4 have different values, and thieves can go in different order.

a0 * k^3 <= n,   

We bsearch on n, for each possible nv, we interate all k from 2 t0 10^5, and add n/k^3 to count. N is good if count >= m;


