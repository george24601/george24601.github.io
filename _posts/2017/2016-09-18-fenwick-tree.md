---
layout: post
title: "Fenwick Tree"
description: "codeforce"
category: 
tags: []
---
Problem to solve
---------
Fast prefix sum calucaltion when there are updates to the underlying trees


1. Unlike segment tree (ST), FT is "flat",i.e., O(n) space complexity

2. Each index represents the segement sum of [index - 2^r + 1, index], notice that index starts at 1, and r is the bit index from right to
left, starting at 0, i.e., each index covers a seg of length 2^r

3. as a result: tree[1] starts at 1 - 1 + 1 = 1, tree[2] starts at 2-2 + 1 = 1, tree[3] starts at 3 - 1 + 1....

4. Therefore, when we are reading, we start taking out the right most 1 bit from the input repeatedly until it becomes 0

5. When we are updating an entry, we keep adding the right most 1 bit from the input until it becomes size of the array

6. To calculate the 2^(right most bit pos), i.e., the shortest bit suffix that starts with a 1 bit,  we can use num & -num!

7. To initialize the tree, we just mark all 0, and then use the write operations to set each index to the input value


459D: Pashmak and Parmida problem
------

iterate through array once, compute frequency for each entry, and populate the FT for frequencency

iterate the array again, from right to left

```
	1. find the current accumulated frequncy for current entry 

	2. update FT, with FT(frequency)-- and FT(frequency-1) ++

	3. use FT to answer prefixsum(frequency), add the result to the final answer

	4. update current frequency++
```
