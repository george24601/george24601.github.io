---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

425A: Sereja and Swaps
--------
consider the case k = 1, of course, we have to swap in the biggest number

Therefore, for all (i, j) combos, sort entries within [i, j] and outside [i,j] separately, swap the first k entries within the band that is
smaller than the outer band


244C: Checkposts
--------
Find SCC of the directed graph, for each SCC, put one post on the one with lowest value. and then put one for each v outside SCCs


518D:  Ilya and Escalator
---------
consider at time t

1. if n - 1 has not boarded yet by t, do nothing

2. if n - 1 has boarded by t - 1, either p board, or stay

pr(n board by t) = p * pr(n-1 boarby by t -1) + (1-p) * pr(n boarby by t - 1)



159D: Palindrome pairs
-------
consider if we have a palindrome, then answer is 

num(i, j) = 0 if a[i] != a[j]

1 + num(i+1, j-1) if a[i] = a[j]

then finally brute force on all (i, j) combos


313C: Ilya and Matrix
---------
each entry appears at least once, therefore, we should force higher numbers to appear more
e.g., biggest number can appear n + 1 times, the follwoing 3 numbers appear n times, the following 3 * 4 numbers appear n - 1 times... 


337C: Quiz
---------
ideally, if we do not trigger the k rule, the final score will be m. We need to limit the times the k rule is trigger as much as possible

of course, we want to trigger all double rules asap, i.e., at the beginning, otherwise, later correct answers will contribute to base

so the final solution would look like
a continous band + bands of size k + a remainder band

L + (k - 1) * i + LR = m 

L + k*i + LR = n

L + k * (n - m) + LR = n

we can bsearch on the length of the contnous band length, find the smallest that works 

for the contnious band, the f(L) (assume L % K == 0) = (f(L-k) + k) * 2

f(L) + 2k = 2 (f(L-k) + 2k)

f(L) + 2k = 4 (f(L-2k) + 2k)....

So we can do fast exponatation here
