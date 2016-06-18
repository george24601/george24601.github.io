---
layout: post
title: "Codeforce round 353: tree construction"
description: "post-mortem"
category: 
tags: []
---

Consider the final result, should the newly inserted be a left child or a right child?

```
  Claim: if it is a left child, then its parent is the tightest upper bound

  Proof: if there exists v < p1 < p, consider the common ancestor of p and p1. 

  If it is p1, then v should go left

  If it is p, then v should go p1

  If it is some other node, which means value between p1 and p, then v should go to left at that ancestor.


```

Similarly, if it is a right child, then its parent is the tightest lower bound

But we have 2 choices, left or right, which one to take?

```

  Claim: we have exactly one choice

  Proof: consider the LCA a of l and r, if l has right child and r has no left child, we know a is not l or r, i.e. a will be a tighter
bound for v then l and r. 
  
  This contradicts our previous lemmas. So we just need to check which one has no left/right child
```

