---
layout: post
title: "Codeforces notes"
description: ""
category: 
tags: [paper]
---

631C: Report
----------
```
1. Start from the tail, and get increasing list of range

2. for the entries outside the maxrange, push them to the end of answer

3. for each entry in the range, push the delta with the next entry from either head or tail depending on sort position

4. For the last entry, turns out we can just set delta to 0
```


622C: Not Equal on a segment
---------
Since it just asks for existance/any, we can just look for an extreme solution, i.e., given a index r, find the rightmost l < r s.t. a[l] =
a[r], this can be done in a linear scan



144C: Anagram Search
---------
Do a sliding window and update the number of deficits and number of ?s, it is a hit if and only if  # of deficits = number of ?s
