---
layout: post
title: "2008 1C: Increasing Speed Limit"
description: ""
category: 
tags: [google-code-jam]
---

Subproblem for small case:

  Given index i, how many j < i, s.t., n[i] < n[j]

This subproblem stops working for the large case, because in the worse case, a sorted array, the summation step will be n^2, i.e., we can not sum one by one
now directly now. This means we need an index-like structure that can represent a summary of ranges, to avoid doing ops across the range one
by one.

-------
Subproblem: 
  
  Given index i, how many subsequence ends before i s.t. the ending element of subsequence < n[i]?

How do we decompose this problem?

A more natural association will be segment tree, i.e., static range + dynamic attached node info. 

Subproblem: 

  1. What should be static range part represent
  2. What should be stored in segment tree node?

Option 1:

range represent range of indices. Then inside tree node, to answering the question, we need to store numeric value information regardless,
to avoid going down all to the leaf.

Option 2:

range represent range of values inside array, similar to 1, we need to store range of indices inside node. But upon closer inspection,
since we are wrapping up from left to right, all frequency will fall in the range j < i. i.e., we can do without it!
------

Pseudo code:

```
  sort the array and construct BST and segment tree, wiht node representing values in the array

  each node has attached node info set to 0

  for i = 1 to n
      get sum of frequncey with range < n[i]
      save result to answers, which represent # of increasing subsequence ending at it
      update tree to include the sum calculated. (if previous step gives 0, set it to 1 instead)

```

 
