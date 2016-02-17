---
layout: post
title: "2015 1B: Counter Culture"
description: ""
category: 
tags: [google-code-jam]
---
Insight A: need to bring the number to the same # of digits,and then work on individual digitso

So the sequence would be 9 -> 10 -> 11 -> 19 -> 91 -> 99 -> 101 -> 109 -> 199 -> 991....

Insight B: after we reach the length we need, we need no more than 1 reverse op

If there exists more than 1 reverse, the op would be:

```
adds -> reverse -> adds -> reverse -> adds ->....
```

We can combine first and third adds for the same effect and remove 1 reverse, i.e., the solution wont be optimal

The general sequence of actions:

```
1. generate to 10*1 with enough digits
2. use adds to form the right half as the mirror of targeted left half
3. reverse, note we dont need to reverse if the left half is of form 10* already
4. use adds to form the right hafl as the targed right half
5. speical case is the # ends in 0, in that case step(N) = step(N-1) + 1
```
