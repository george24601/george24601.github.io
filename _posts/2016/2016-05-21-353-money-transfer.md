---
layout: post
title: "Codeforce round 353: Money transfer"
description: "post-mortem"
category: 
tags: []
---

During the contest, i realize that each edge will be passed only once: because

```

  1. if there is back and forth, there is no need to transfer at all

  2. If two are in the same direction, then one can wait until all have arrived
```

This means that the order of operations become delicate and hard to attack, which stuck me.

Instead, I should have focused on the end result => in the end, what edges are passed?

We can decompose the cycle into disjoin bands, where transfer happens only within each band. This means each disjoin band has total 0 =>
we have successfully decomposed the problem!

I also realized that I can approach the final value in a different direciton, i.e., instead of constructing touched edges, and I can looked
at unpassed edges => this shows that answer = n - # of bands

So we just need to partition the cycle in a way to max # of bands, whose sum is 0

Here is the proof for the prefix sum approach

```

 1. if a segment [i, j] has sum 0, then we know prefixSum(i) = prefixSum(j)

 2. Therefore, for one way of partitioning into bands ends at a, b, c.., we will have prefixSum(a) = prefixSum(b) = prefixSum(c)

 3. This means that each prefixSum value is the signature of partitioning, with the frequency of that value as number of bands

 3. To find the max number of bands, we need to locate the signature with highest frequency.
```


