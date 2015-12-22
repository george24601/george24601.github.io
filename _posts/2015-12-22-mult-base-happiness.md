---
layout: post
title: "2009 1A: Multi-base happiness"
description: ""
category: 
tags: [google-code-jam]
---

Insights:

1. A number is either happy or not

2. The reduction to 1 forms a trail. All numbers on that trail will be happy

3. The reduction progess cares only set of numbers, i.e., given a set of digits, any permutations of that is happy (of course 0 can not be
first digit)

4. Notice there is < 2^9 possible answer sets, we can just do pre-computation. Moreover, since it is a set, we dont have to generate all 2^9
answers! we can just generate subsets of them, and combine the result with unions and intersections

Why I got stuck
--------

1. I had problem understanding the upperbound of the number, turns out brute force search alone is enough to cover it. What I should have
done is to brute force the small case, and guess/experiment from that

2. I thought a number is more likely to be unhappy, since the final step gives any digit between 1 adnd 9, i.e., 10 % chance to be happy(roughly). So
we should generate set of integers based on set of digits forming a happy number. Turns out I ignored the square part in the
computation,i.e., this assertion is false

3. Another key insight is that, similar to a lot of self-reinforcing operations, e.g., on a group. If the chain keeps going, either we end
up with identity or itself. So instead of search for happies, we can look for unhappiniess


Pseudo code
-------

For num from 1 to 20 * 10^ 6
  keep a bit map on what bases are happy

  For each base from 2 to 10
      decide if num is happy under that base, with the caching previous result
      update the bitmap

  based on the bit map, update the final answer map

output the answer map

The code that generates output is just a read-lookup loop



    
    
    


