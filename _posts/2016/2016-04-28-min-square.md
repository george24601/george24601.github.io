---
layout: post
title: "SRM 614: MinimumSqureEasy"
description: "topcoder"
category: 
tags: [topcoder]
---

There is a relationship between square and points. This means we can explore the relations from sqaure to points, or to squre from points.
For the completeness of our arguments, we have to try both.

From square to points
---------
```
  1. notice that we have only 3 * 3 * 3 * 3 different choices of borders, we just generate them all. Note what we generate is rectangle, so
need to transform it back to square at the final step

  2. for each generated rectangle, we pass through the list of points, see how many are inside it

  3. For each rectangle that includes at least n-2 points, we need to enlarge it to a sqaure,i.e., the max of 2 border lengths
```

From points to square
---------
```
  1.notice we have only 50 points, so we can pick the 2 points that will not be included in the square. Know that this complete search
covers all possible solutions already,i.e., our final solution must be within one of these cases

  2. after taking out the 2 points, calculate the max sqaure that covers ALL remaining points, i.e., it will be max(maxX - minX + 2,
maxY-minY + 2)

  3. repeat 2 for all choices we pick in 1), and update optimal value along the way

```

In my expreicne, the points to square approach is easier to code
