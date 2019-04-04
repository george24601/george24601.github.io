---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

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
