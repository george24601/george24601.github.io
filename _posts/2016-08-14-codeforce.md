---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

367A:  Sereja and Algorithm
--------
For string s if an algo can terminate, we know exactly |x| , |y| , and |z|

```
  1. if len % 3 = 0, |x| = |y| = |z|

  2. if len % 3 != 0, |x|, |y|, |z| can not differ more than 1

```

Claim: if a string satisfies the condition above, we know algo on it terminates

Proof: induction

```
  1. base case n = 1,2,3, obvious

  2. when len % 3 = 0, we keep the last letter, and apply the inductive hypothesis on the len - 1 substring, and we add that letter back to
the tail and will form a terminating string

  3. when len % 3 = 1, find the longest prefix s.t. |x| = |y| = |z|, apply the inductive hypothesis on the prefix and suffix, and then move
up the head of the suffix to head if applicable

  4. when len % 3 = 2, find the longest prefix s.t. |x|  + 1 = |y| + 1 = |z| or |x| +1 = |y| = |z| + 1, apply the hypothesis on both parts

```

479D: Long Jumps
--------

Need 0 marks, if there exists a(i) + x, a(j) + y already

need 1 mark, if 

```
  1. 1 already exists from existing mark

  2. m s.t. abs(a(i) -  m) = x and abs(a(j) - m) = y. We can generate by one condition and then filter by the other, i.e., can do a linear
search on points, both direction with delta x, and then filter out those outside [0, l], and m +/- y is not part of existing points

```

Otherwise, need 2 marks.

103B: Cthulhu
------
Assume the graph is connected

This is similar to the sunny graph problem,i.e., when |v| = |e|, <=> each node has only 1 parent in directed graph, and note that the cycle has min length at least 3, so that is satisfied as
well


617D: Polyline
-------
If there is only 1 line, then they share all x or y, if and only if

If there are 2, then at least 2 share x or y, however, if we have shared x or y, we need do make sure z has the other axis which does not
fall into the range of (xx, yx)

To make coding easy, we can do a bruce force search on the first line.


628B: New Skateboard
--------

Idea 1: 
------
num [i] [mod] = number of substrings ending at i, with mod as the substring # % 4

num[i + 1] [(mod * 10 + digit) % 4] += nums[i] [mod]
num [i +1 ] [digit % 4] ++

Idea 2:
-------
if number % 4 == 0, <=> the last 2 digits form a number % 4 == 0, therefore, we look for ll 2-letter substrings whose num % 4 == 0 , and add length of prefix
to final ans
