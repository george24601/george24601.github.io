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
when n - m >=  ceil(m/k), we know we can arrange k in the ceil(m/k) bands, none of which triggers the double rule 

when k = 2, 12 34 gives score 4 + 4

1 234 gvies score 1 + 10, i.e., we need to reduce number of longest consecutives. Moreover, the longest band and shortest band size diff can
no more than 1, otherwise, we can take 1 from the longest and give it to the shortest, and still imporve the solution, and we have lower
bound for S

(m/S) + m <= n => S >= m / (n - m)

