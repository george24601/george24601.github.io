---
layout: post
title: "Code Jam 2008 1A: Minimum Scalar Product"
description: ""
category: 
tags: [how-to-solve-it]
---

I discovered the insight from the 2 element, and to an extend, 3 element case. But how to prove it?

```
Suppose our current permutation is not as desired, we start changing this into our target, from the smallest to larget, one element at a
time. 

We will fix the element by connecting to the correct one, and connect the now 2 empty ones. The shape of these changed connections are same
as in the 2-element case. Thus, each step only reduces the overall sum,i.e., for any perm, by transforming into our target perm, the sum
will only decrease => Our arrangement is optimal
```

Notice the idea of steps of transformation vs final states. This kind of duality can be seen in the log-state duality
