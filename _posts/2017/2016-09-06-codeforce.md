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


689C: Mike and Chocolate Thieves(!!!)
----------
a * k^3 <= n, assume k = 2, 3, 4..., and we know m =  n/8 + n/27 + .... + 1, Note that m > n/8, therefore, n < m * 8

I had difficulties proving n's upperbound. Also notice we are looking for minimal viable answer here, so need to do extra check at bsearch's midpoint

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
