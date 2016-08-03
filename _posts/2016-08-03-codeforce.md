---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

343B:  Alternating Current
-----------

After looking at smaller cases, we can see that we can reduce consecutive ++ or -- pairs, until the whole string is reduced to empty string.

Therefore, we can scan from left and right, and eliminate all ++ or -- as we scan through. However, we can not afford to modify the
underlying strng. Therefore, we have to maintain a prev[i], i.e., a linked list

as we scan through , if s[i] = s[prev[i]], we set prev[i+1] = prev[prev[i]],  i.e., we remove i and prev[i] from the linked list

Init prev[i] = i-1 in [0, n], linked list will be empty if prev[n] = -1, i.e., the init value for prev[0].


198B: jumping on walls
----------

Consider the subproblem by dropping the condition:  what if without the rising water, how to solve it?

We can reduce to a graph connectivity problem, which can be solved with DFS

```
  bool visit(height, side)

      mark (height, side) as visited

      canEscape = height > n - k

      canEspace = canEscap || visit (height + 1, side) if it is not visted yet.

      canEspace = canEscap || visit (height - 1, side) if it is not visted yet.

      canEspace = canEscap || visit (height + k, !side) if it is not visted yet.

      return canEscape

```

But with the rising water, how to prove the existance of a solution?, 

````
 1. we know the time arriving at a spot must be before water reaches that level. 

 2. Therefore, we look for the earliest time to arrive at a spot. A solution exists iff we can find an earliest time to escape

 3. This transforms the problem into a shortest path in a unit length edge graph problem -> a BFS would do

 4. note that since if we go up, we go at least 1 unit up, we do not even need to worry about water in that case
  
 5. However, if we go down, we need to ensure water level is not high enough yet,i.e., earliest time at the spot = water at that time <
height of the spot
```
