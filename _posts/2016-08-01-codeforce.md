---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

66D: Petya and His Friends
---------
Obviously, a(i) can not be a prime, otherwise, it will violate 2nd constraint

One construction would be a(i)  = product of first n primes / ith primes

Another construct is to look at case of N = 3 (N = 2 obviously gives no answer), can you expanding to N with a known solution at N = 3


353C: Find Maximum
--------
Note that m <= 2^n - 1

Notice that all a[i]s >= 0, so we should be greedy and include as many #s as possible. So we should interate through the prefix of m

at each i, if m[i] = 1, update suffix sum, update result with prefix sum + suffix sum , update prefix sum


343A:  Rational Resistance 
--------
Note that this problem does not allow joining 2 elements. Therefore, we can just backtrack. 

```
  1. we are done if a = b

  2. if a/b > 1, then we reverse n steps so that a/b - n <= 1

  3.  if a/b < 1, then we reverse n steps so that b - n * a <= a

  4. Note that at recursive call we backtrack n steps, in the spirit of GCD algo

```

316B2: EKG
-------

```

  1. if prev[a] = b (b != 0), populate next[b] = a;

  2. for each prev[a] = 0, go through the linked list to find its length, add it to a vector size if it does not have the target

  3. possible [v][i] = possible[v] [i - 1] || possible [v - size[i]][i - 1]

  4. iterate through all possible[v][size.lenght], print v + pos if it is true

```


150B: Quantity of Strings
--------

if k = 1, any string would do

if k = n, any string of length l/2 would do

Claim: if k is even, then the string can have only 1 letter

Proof: suppose k = 2* j. Consider the substring starting at 1 

```
  1. a[j -1] = a[j] because they are the inner most pair

  2. a[j -2] = a[j], 2nd inner most pair

  3. a[j-3] = a[j -1] = a[j], 3rd inner most pair
  
  ....
```

Claim: if k is odd, then the string can have only 2 letters, all odd chars are the same, and all even chars are the same

Proof: suppose k = 2 * j + 1, Consider the substring staring at 1

```
  1. a [j + 1] = a[j +3] =  = a[j - 1] , inner most pair


  2.a [j] = a[j+4] =  a[j - 2]

  3. repeat steps for substring staring at 2..... n -k

```

