---
layout: post
title: "An interesting problem I came across"
description: ""
category: 
tags: []
---

```
Given an integer array a, find a way to slice it into 5 parts, such that the maximum sum the sliced subarray is minimized.

Conditions:
1. a[i] is from -1e9 to 1e9
2. After we slice the array, each part should not overlap, and there is no gap between the parts
```

Assume the array index is 1-based.

Idea 1: Brute Force DP
--------------
keep maxV(i, numSeg) = minium of maximum sum of the parts, after we slice the subarray 1...i into numSeg parts.

At each step, we check all j < i , and try update maxV(i, numSeg) with max(maxV(j, numSeg -1), prefixSum(i) - prefixSum(j-1)), if such value
< current max(i, numSeg)

Overall complexity is O(n^2), final answer is maxV(n, 5)


Idea 2: Binary Search + Greedy
-----------------
```
1. binary search on the possible optimal value v, for each v, we check if it is possbile to cut the array such that sum of each part is no
   more than v

2. Then we start scanning a from n - 1 to right, and find the first i such that prefixSum(i) >= prefixSum(n) - v; 

3. we keep reducing i, until we the first j, j < i, such that prefixSum(j) >= prefixSum(i) - v 

4. we keep reducing i, until we the first k, k < j, such that prefixSum(k) >= prefixSum(j) - v 

5. we keep reducing i, until we the first l, l < k, such that prefixSum(l) >= prefixSum(k) - v 

6. If i reaches 0 before we locate all 4 cuts, or the prefixSum(l) >= v, then this v is not feasible.

```

Overall runtime O(logV * n)

Why idea 2 works?
----------------
```
1. We can binary search the target value v, because we know if a v2-cut is feasible, and v2 < v1, then v1 cut is feasible too,i.e., this
   function v => feasible has a clear line separating the feasbile and infeasible regions. Since the two regions don't intersect , we can
binary search for the separating line,i.e., the optimal value.

2. For the greedy approach, assume we have a feasible cut solution, then the parts 1...5 must satify the following conditions:

    prefixSum(i1) <= v
    prefixSum(i2) <= prefixSum(i1) + v
    .......
    prefixSum(n) <= prefixSum(i4) + v

So we need to search for solution, but how do we search?

3. Note that if our algorithm returns a result, then it is feasible for sure, the case we need to defend against is that we may return
   infeasible even if one solution exists. Therefore, every time we want to take a cut, we need to be as conservative as possible, so that
we can leave maximal flexibilities to the follow-up steps 

4. By this reason, if we take the rightmost segment that satisfies our current condition, we know for sure we didn't move too far to the
   left so as to skip a cut that is valid for the follow-up steps, because we have to address the current cut before we can move on to the
next cut. 

5. By 3 if we discover one, we know such solution is feasible. By 4, if we don't discover one, one solution must not exist. This means our
   algorithm is sound and complete, i.e., correct
   
```

Formal proof
-----------
We need to prove that if our greedy algorithm returns infeasible then it is impossible to have a valid cut for the array with target value
v

```
Proof by contradiction: suppose there exists a valid cut at (c1, c2, c3, c4), 

suppose our algorithm discovers cuts (o4,o3,...) with o(i) being the last cut that we discover. 

Claim: o4 >= c4, o3 >= c3, ....oi >= ci, and our algorithm can discover all 4 cuts

Proof: By induction, from right to left
Base case: o4 >= c4, directly from the code

Inductive case:
when o(i + 1) >= c(i + 1), if we can not discover a cut between [c(i + 1), o(i+ 1)), then our algorithm will discover o(i) >= c(i), because
prefixSum(c(i+ 1)) = prefixSum(o(i + 1)), i.e., when we are looking for >=  prefixSum(o(i + 1)) - v, we are actually looking for prefixumSum(c(i + 1)) - v. Therefore, either we find o(i) before c(i), or we discover right at the c(u)

=> direction contradiction

```

Pseudo code
------------
```
curPS = prefixSum(n);
discovered = 0;

for i from n to 1
  if(prefixSum(i) >= curPS - v){
      discovered++;

      if(discovered == 4)
        return prefixSum(i) <= v;
      else
        curPS = prefixSum(i)
  }


return false;
```



