---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

599C: Day at the Beach
------

Suppose we find our first segment s with length l, what property will that have?

```
  1. it will contain the l smallest numbers. Conversely, if we find the first l smallest numbers, we can declare a segment

  2. any remaining numbers >= any numbers in the s,i.e., the min of the remaining segment >= max of s. Conversely, having this property
means s must have the smallest numbers,i.e., can form a segment

```

This leads to 2 ideas

Idea 1
------

We keep track of a balance of numbers as we scan through the array from the left, every time we hit with balance 0, we announce that we detect a segment


Idea 2
-------
We scan from left to right, every time we detect i s.t. max(1...i) <= min(i+1...n), by property 2 we can declare a segment



123A:  Prime Permutation
-------
Consider the target string: 

All s[i] where i | 2 will share the same letter

if n >= 6, then i | 3 will share the same letter as previous, because s[6] = s[3] = s[2] 
Similarly, if n >= 10, then all i | 2, i |3 , and i | 5 will share the same char

By induction, we know for all prime <= n/2, i | prime will share the same letter. Moreover, if a composite number <= n/2, it must have a
prime fact <= n/2, i.e., all number in [2, n/2] must share the same number

Conversely, if prime > n/2, it can appear only once as factor, i.e, at i = prime itself.
if composite > n/2, then it must be a prime factor <= 2/n, which means it will share char with previous steps


So the algorithm is

```

  1. scan string to keep track of counts, and which letter is the most popular

  2. all number in [2, 2/n] must be same

  3. for numbers in 2, use a process similar to sieve to generate composite numbers > n/2 that has to share the same char

  4. scan through the answer again to fill in whatever left

```

297B: Fish Weight(!!!)
--------

Idea 1
-------
Consider the delta between A and B, it is possible for A to win if and only if

```
  1. delta of the biggest fish is in the favor of A

  2. first continous positive delta in the favor of A > sum of continuous positve delta previously in the favor of B.
```

But coding this is not the easiest thing

```
  1. sort <weight, count> pair in decreasing order for A and B

  2. if top weight (A) > tw(B) || top weight(A) = tw(B) && count of tw(A) > count oftw(B), done

  3. if top count and weight same, progress both indeies 

  4. progress B indice, until conditions 2) is statisfied, update the top most count for B

  5. now progress A index until 2) is violated, update the + balance for A

  6. compare if + balance is > - balance
```

Notice here we need to iterate through map <int, int> which is often sigh of flawed algorithm

Idea 2
---------
just sort all fishes caught, on the first difference, if A is winning we are done. If B is winning, we need to keep scanning until we find a
position where A is winning

Claim:  A is winning in at least one postition <=> it is possible to find a solution

```
  1. (<=)  if A is not winning at any position, obviously impossible 

  2. (=>) we can make the delta arbitarily large to cover all potential balances

```
