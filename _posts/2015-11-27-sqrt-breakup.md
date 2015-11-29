---
layout: post
title: "Code Jam 2008 1B: Mousetrap"
description: "interval tree"
category: 
tags: [google-code-jam]
---

Started with the last element remaining, we can reconstruct the whole sequence of deletion by DP => N^2

Solve the problem differently, instead of last to first, we can go first, and we notice that we can easily determin the initial position of
each card, since by construction,  you know exactly the interval between prev and next position, but brute force construciton doesnt give
better run time.

What is blocking us is that we have to go through index one by one JUST to collect count, i.e., we need to introduce additional index-like
data structure which contains count-related info. 

We can use the classic sqrt break down, i.e., sqrt indices, each represents state of sqrt of rows. Both are sqrt to make sure worst case
works are equiavlent in both cases. This solution can barely fit the run time

A related idea is to use interval-tree like data structure to answer the query, i.e., instead of 1-level indexing, use multi-level indexing:

    Given current index i, what is the next index after skipping j unoccupied indices?

Segment tree's idea is similar.  Note the true segment tree is used to answer range queries, and is not supposed to change after creation.





-------
This problem shares a lot of similarity with Josephus problem. The sqrt trick would work as well
