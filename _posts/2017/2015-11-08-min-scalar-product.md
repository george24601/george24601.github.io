---
layout: post
title: "Code Jam 2008" 
description: ""
category: 
tags: [google-code-jam]
---

### 1A: Minimum Scalar Product

I discovered the insight from the 2 element, and to an extend, 3 element case. But how to prove it? Direct induction does not work well.

    Suppose our current permutation is not as desired, we start changing this into our target, from the smallest to larget, one element at a
time. 

    We will fix the element by connecting to the correct one, and connect the now 2 empty ones. The shape of these changed connections are same
as in the 2-element case. 

    Thus, each step only reduces the overall sum,i.e., for any perm, by transforming into our target perm, the sum
will only decrease => Our arrangement is optimal

Notice the idea of steps of transformation vs final states. This kind of duality can be seen in the log-state duality and gradient descent

We can also see the idea of step-wise, gradient-descent like optimaization approach in the next problem!

### 1B: Crop Triangles

Unknown: number of 3-point sets

Known: list of points

Conditions:

    1.sum of x-coordinates of the 3 points is a multiply of 3
    2.same as y
    3. list of points is generated from a hash-like function with parameters

------

Subproblem: consider only the x coordinate case

    1.From unknown: it is equivalent to x1 + x2 + x3 mod 3 = 0
    2.We realize mod operation represent a group operation with size 3,i.e., any 3-element check is reduced to a pattern of 3 elements from
the group
    3. matching from numbers to groups is too hard, so we will try matching from group to numbers. i.e., find patterns in the group that
satisfies conditions, and see what elements can be reduced to patterns

Now consider y coordinate case: same arguments apply.

------

Subproblem: given (x,y) in the group of size 9, how many 3 element sets I can find to meet criteria?

Answer: since size is only 9, we can just brute force

------

Subproblem: after we identified good patterns, how do we know how many point sets can be reduced to such patterns?

Answer: 3 cases 

    1.if 3 points are reduced to the same element in the group
    2.if 2 points are reduced to the same element in the group
    3.if 3 points are reduced to 3 different elements in the group
    
Adds the answer for each 3-elemnet pattern together and we have the answer


------

Problems I ran into:

    1.memset a unsigned long long array gives problem(?)
    2.One instance should use long long to avoid overflow
    3.We are looking for sets, but the counting we did is for combinations, i.e., same sets are repeated many times in different orders. I
missed one case
    4.0 requiremnet is not satisfied because all its positions are 1 already, this means we have to set the remaining pos 1 from 0 to meet the 1
requirement
    5.1 requirement is not satfisfied, because that pos is 0. Continue with argument from the previous step, we set that bit to 1. 
    6. repeat 2 - 5

