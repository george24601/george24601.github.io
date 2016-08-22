---
layout: post
title: "Codeforce Notes"
description: "codeforce"
category: 
tags: []
---

436A: Feed with Candy
------
Consider the final solution: we should never eat 2 in a row. Therefore, we just try 2 cases, the first we eat is type 1 or type 2, and pick
the best answer.

407A: Triangle
------
we can fix one point at (0,0), and then brute force to search for the second point coordinate with the a conditon, and then use the b
condition to locate the 3rd point, or use the fact that (x, y) and (-y, x) form a right angle.

The part I failed is that I forget to check if the 3rd edge is parallel to any axis!.

460C: Present
------

Idea 1
--------
Bsearch the answer, for each answer, we linear scan the array from left to right, also, we need to maintaind a d matrix to record the stop watering event

Idea 2
-------
Greedy works on O(nm), but need additonal DS to speed it up


407B: Long Path
--------
Claim: when we arrive at i + 1, all previous i has been visited even times already

Proof: By induction, 

```
  1.  when we reach the i for the first time, by inductive hypothesis, prevous i-1 has beenn visted even times 

  2. if i points to itself, it will loop until it reaches even time, then move onto i+1

  3. if move to previous entry, then following sequence of action is same as p visited for the first time. By the difference of the
induction on case p and case i, we know the number of ops introduced by the subseqenct actions at each cell is even
```

Therefore, when we later on move back to previous entries, the # of actions is same as we visit it for the first time, because even times case = 0 times case

visit(i,j) : number of steps to visit j, from i for the first time, with all cells visted even times already, with the exception of i 

visit(i, j) = visit(i, j -1) + visit(p(j-1), j -1)  + 1 //1 for moving from j-1 to p(j-1)

base case visit(i, i) = 0



