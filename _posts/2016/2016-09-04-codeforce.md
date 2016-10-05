---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

567D: One-Dimensional Battle Ships
----------------
For each insertion, we update the max possible # of ships, by finding its lower and upper bound, and calculate the delta. When we reduce the total # < k, we know it is bad

Note that we probably want to storage segments instead of points, because both lower_bound and upper_bound do not return number < current
one, unless you do iterator-- yourself



552D: Vanya and Triangles(!!!)
--------------
We are interested in finding number of points in a same line. We use the idea of the equivlanet class,i.e., we iterate all 2 point pairs, calculate the line between it, 
and use map to store the accumlation result of that line. The offical result uses a hash function to avoid saving the (a,b,c) tuple of a linear equation

One thing to watch out for is the 


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
