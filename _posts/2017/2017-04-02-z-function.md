---
layout: post
title: "Z-Function: Notes form e-maxx-eng"
description: ""
category: 
tags: []
---

z[i] = the LCP between S and the suffix of S starting at i

```
1. Index is 0-based

2. z[0] is not well defined, normally not a problem

```

We keep track of (l, r), the rightmost segment that matches the prefix of the string so far, and now we process i

```
1. if i > r, then compute z[i] in naive algorithem, and upate (l, r) if z[i] > 0, because the new r = i + z[i] - 1 

2. if i <= r, note that s[l..r] = s[0...r-l], so s[i...r] = s[i-l...r-l], so to compute z[i], we can skip the first z[i-l] chars starting
   from z => they will be match anyway, provided that i + z[i-l] - 1 <= r. Otherwise, we can only skip the first  r - i + 1  chars

3. after what we skip, we will have to compute the new entries and update (l, r)

```

Implementation Details
-------------

```
1. l, r, z[0] are all inited to 0, we start populating z[i] from 1

2. we update (l, r) only when i + z[i] - 1 > r

```


Runtime proof
------------
Case by case analysis, prove that in each iteration of the trivial algorithm, we will increase r



Usage: search text for pattern 
--------
```
1. Contatinate a new string s.t. s = pattern + speical char + text. 

2. Calculte z-function for the new string

3. For i in [len[p] + 1, len(s) + len(p)], check if z[i] = len(p). Note that z[i] can not be > len(p) due to our speical character rule 
```

Usage: number of distince substrings in a string



Usage: String compression
---------
Given a string of length, find a string t of shortest length s.t. s can be represtned as a contactenation of one or more copies of t.

loop throug all i s..t. i divides n, stop at the first i s.t. i + z[i] = n, then the strin s can be compressed to the length i

 
