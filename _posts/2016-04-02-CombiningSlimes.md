---
layout: post
title: "SRM 669: CombiningSlimes"
description: ""
category: 
tags: [topcoder]
---

After trying out sample cases by hand, it seems the order of operations doesnt matter much => always same result!

Try to prove that the order of op does not matter by inductions => does not seem to work

So instead of focusing on the sequence of actions, we will focus on the results of these actions, and we noticed that the end result has a
closed form

```
sum of

i * S - i, for all i in S

```

Instead, we prove this closed form by inductions => should we focus on first step (bottom-up), or last step (top-down) ?

```
base case: 2

on n + 1 case,

the last step of merge will give score (sum of set A) * (sum of set S - A)

for each element in A, a

by induction, the previous steps leading to form A means we have accumulated all a * A - a

By carrying out the formula, we will accumulate new scores in the form of a * S - A

Combine the above 2 lines, we know all a * S - a are in the final score, and cost(A) + cost(S -A) + (sum of set A) * (sum of set S - A) =
cost(S)

```
