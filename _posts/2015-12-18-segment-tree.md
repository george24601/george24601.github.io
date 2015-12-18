---
layout: post
title: "Segment Tree"
description: ""
category: 
tags: [algorithm]
---

Basic idea: 

  1. A binary search tree,i.e., each node has 0 or 2 child nodes, which is static 

  2. By static we mean the shape of the tree and the value of each node,i.e., we do not have to worry about re-balancing the tree. We

  3. This means normally we construct a balanced BST during init, and do logn querying/updating attached node info as part of op

  4. Due to the nature of the tree, the parent node info can represent a summary of the tree. This enable effiicent range queries, and
cascading update will cost logn, i.e., # of level in a balanced BST.

  5. Implementation wise, since we can perfectly balance it during it, we can use a dense array implementation,i.e., root node at 1, left
child index *2, right child : index * 2 + 1
