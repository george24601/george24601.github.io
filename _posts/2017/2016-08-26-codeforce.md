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


### 518D:  Ilya and Escalator
O(n^2) dp, special case at cnt = 0 or cnt = n



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

L + k * i + LR = n

L + k * (n - m) + LR = n

we can bsearch on the length of the contnous band length, find the smallest that works 

for the contnious band, the 

So we can do fast exponatation here

f(n) = 2^(n-1) * f(1), where f(1) = 2 * k + 2 * k

#### Mistakes I made during implementation

  1. During fast expontation, the resursive call should appear only once
  2. During mod operations, need to defend against negative logs 
  3. This means for those easy-to-generate cases, I should run the max case myself just to ensure the sanity
  4. In bsearch, need to delete if mid == low, if we want to be greedy to push it down further, because low <= mid < high 


558C: Amr and Chemistry
---------------
Applying 1 and then 2 will have no effect. Therefore, all 2 ops must appear before 1)

Binary search the final answer may not work, because the result function is  NOT monotonic!

In the final solution, the form can not have all 2s or 1s as the last step

if the final value is odd, then either all applies 2 or they are already like that
the final value must be of form 1xxx10..0, the 1xxx1 part must be a prefix for all numbers.
Therefore, we find longest common previx for all numbers, that will be the prefix of the equal value as well, and then we can bruteforce on number of appending 0s


557C:Arthur and Table
------------
Suppose final max len is L, all l > L are removed, all l < L we keep all but |L| most expsive ones

We start from smallest length and maintain a heap, On reaching a new final length, we can

```
	1. get top |L| expensive ones from the heap, these are the ones we save
	
	2. calculate total = TotalE - bandE - saveE

	3. add this band to the heap

```

We iterate through it and will get the final answer


137C: History
--------
Note that we just need to decide if a event can be within any other event
We need to sort by a[i] first, to eliminate one condition, and then when we scan through, we need to keep updating the rightmost endpoint


303A: Lucky Permutation Triple
-------
So we can fix the first perm as 0...n-1

n = 1, just 0

n = 2, no answer

n = 3, (0, 1, 2) + (2, 0, 1) or (0, 1, 2)

ease to prove by induction than when n is odd, we just force b(i) = a(i)

(!!)How do you prove that a solution does not exist when n is even?


535C:  Tavas and Karafs
--------
Suppose we have (l,r), to be eaten, we need to have sum of height(l,r) <= mt, also height(r) <= t

Claim: this condition is enough
Proof: induction on t
t = 1 obvious

t = n, we apply the m-bites on the hightest entries
if band within >= m, the inductive hypotheis is kept, and we can use t = n -1 case.
if band within <= m, we know we can reduce all highest by 1, and since height(r) <= t, we can just eat them all

so we can bsearch for the highest r that satisfies that


297A: Parity Game
--------
op 1 means that in the final result, # of 1s is increased by at most 1, if odd. or at most stay same if even. Therefore, if number of 1s in
b > # of 1s in a + 1, impossible

consider a = 1, what b strings can we generate? => any string with 1 or 2  1s, and any a string with at least one 1 can be reduced to 1

consider a = 11, any b strings with <= 2 1s can be generated, because a can be reduced to 1 as well 

procedure

```
  0. if number of 1s is odd, apply op 0 to make it even

  1. fit the 0 prefix of b by applying op 0

  2. apply op 2 until it removes the first 1 in a

  3. apply op 1 so that the 0...01 prefix in b becauses a suffix in a

  4. now we can repeat ops 1-3 to make all 0...01 segs match, and finally use op 2 to remove redundant A prefixes

```
