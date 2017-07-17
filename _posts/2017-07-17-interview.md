---
layout: post
title: "Interview Problems from careerCup"
description: ""
category: 
tags: [interview]
---

Problem A
--------
Given an array of integers, return true if there're 3 numbers adding up to zero (repetitions are allowed)
{10, -2, -1, 3} -> true
{10, -2, 1} -> true -2 + 1 +1 =0

Idea
------
Standard read and then update pattern problem

```
Set<int> twoSums 

LP(i, 0, n){
	if(twoSums.count(a[i])) 
		return true
	else
		LP(j, 0, i)
			twoSums.insert(a[i] + a[j])
}

return false
```


Problem B
-------
You're given an array of integers(eg [3,4,7,1,2,9,8]) Find the index of values that satisfy A+B = C + D, where A,B,C & D are integers values in the array.

Eg: Given [3,4,7,1,2,9,8] array
The following
3+7 = 1+ 9 satisfies A+B=C+D
so print (0,2,3,5)

Idea
-------
Simliar to the problem above. Try all O(n^2) combinations, put it ina map<int, PII>. Note that we need to keep track of only one pair, because 
```
1. we will terminate early

2. For all feasible combinations, we just look for the one with lexigraphically minimal (A, B). Our solution will not miss, because given anothing solution, if they involves indices with same value of A or B, we can swap them with the first A or B we met => which is discovered in our loop
```  


Problem C
-------
Given an undirected graph and a node, modify the graph into a directed graph such that, any path leads to one particular node.

Idea
-------
DFS variant. 

Test cases
-----
```
1. 2 node path
2. 3 node simple path
3. 3 node cycle

```

```
void dfs(v, from){
	if (visited[v])
		return
	else
		visited[v] = true

	LP(i, 0, g[v].size()){
		int u = g[v][i]
		dfs(u, v)
	}

	removeDir(v, from)
}
```


