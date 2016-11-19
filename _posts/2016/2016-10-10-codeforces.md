---
layout: post
title: "Code forces notes"
description: "Study question"
category: 
tags: [paper]
---

161D: Distance in Tree
--------
cacluate n(v, d) = number of nodes with has distance d from node v

Then the answer will be sum of, for each v
```
1. n(v,d) 

2. n(cv1, m - 1) * n(cv2, d - m - 1) 

3. Note the second part = n(v, d- m) - n(cv1, d- m -1), i.e., we need to go down a different path. Therefore we exclude the cv1 itself

```

369C: Valera and Elections
----------

```
1. n nodes and n-1 edges suggest a tree

2. therefore, we can start from root 1, and dfs, we elect its immediate child if its subtree does not elect anything, and the child-parent
edge is broken

```



429A: Xor-tree
---------
just be greedy from root, and recursive traverse through the tree. 


510C: Fox And Names
----------
The corner is the impossible case
```
1. if the dag has a cycle, i.e., the v is marked as visited, but its post number has not been inited yet

2. if the second string is the prefix of the first string

```

