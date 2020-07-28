---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

451C: Predict Outcome of the Game
-------
Given we have the value of 2nd, we have 4 combos for the +, - of d1 and d2, therefore, we can calculate the sum of v1, v2, and v3. We just
need to ensure that each v is in [0, k] 


5C:  Longest Regular Bracket Sequence
----------
Maintain a stack , with each entry knows which index it is from. when we pop the entry, we update the length or the length count

However, know that the length will be from the SECOND top, because that the last time we do not have a regular string. Otherwise, the composition case ()() will fail!


416C: Booking System
----------
Consider the request with max p(i), and can fit into a table, we should accept it. Otherwise, we can improve the solution

Therefore, sort requests by p(i), and then get lower bound for each pi, if lower bound does not exist, reject


710C: Magic Odd Square
----------
At row i <= n/2, E * i , and then n - 2 * i O, and then E * i again

At row i = n/2 + 1, only Os

At row i > n/2 + 1, E *(n -i), then 2 * i - n, O and then E * (n - i again)

We can prove by induction this arrangement is correct


567D: One-Dimensional Battle Ships
----------------
For each insertion, we update the max possible # of ships, by finding its lower and upper bound, and calculate the delta. When we reduce the total # LT k, we know it is bad

Note that we probably want to storage segments instead of points, because both lower_bound and upper_bound do not return number LT current one, unless you do iterator-- yourself



### 552D: Vanya and Triangles

Seems the a brute force n^3 solution works, i.e., are 3 points on the line at all? 

Since two points are enough to define a segment, we can just calculate the equivalent class of its segments, and deduct combination counts. The trick is how to calculate the hash of the segment??!!


632A: Grandma Laura and Apples
----------
Just reverse the seqence of operations, and first number = 0

```
if current is half

  ans += number * p

  number *= 2

else
  
  ans += number * p  + p / 2

  number += number + 1  
```


204B: Little Elephant and Cards
-----------
Calculate number of each color. If such answer exists, we know max we have 4 possible canadidates,
and then brute force each color => calculate # of face ups and only face downs, and get the minimal answer 


237C: Primes on Interval
----------
Sieve to generate all primes <= 10^6,

start from the first prime >= a, check if the a+k entries is still within the range. If it is , update the difference as answer


442B: Andrey and Problem
---------
If pick only one, obviously, pick the one with highest prob.

Given we have a set, we can write down the exact formula. It becomes obvious that we can add a new memeber to set and increase overall
probability as long as sum( p(i) / 1 - p(i)) > 1

So we can sort p(i)s and include them until that S <= 1
