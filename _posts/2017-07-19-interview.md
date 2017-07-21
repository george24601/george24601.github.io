---
layout: post
title: "Interview Problems from careerCup"
description: ""
category: 
tags: [interview]
---

Problem A
--------
Reverse a linked list with O(1) space

```
pair<Node, Node> rev(Node h) {

	if (h.next == null)
		return pair<Node, Node> (h, h)
	else{
		nh, nt := rev(h.Next)
		nt.Next = h
		h.Next = null
		return PNN(nh, h)
	}
}

```


Problem B
--------
print rows of a binary tree, terminating each row with a carriage return

```
depth[root] = 0

toExpand.insert(root)
lastVisit = null

while(toExpand.size()) {
	int curV = toExpand.top();
	toExpand.pop();

	if (lastVisit != null && depth[lastVisit] == depth[curV] - 1){
		cout << endl
	}
	lastVisit = curV;
	cout << curV;

	foreach neighbor in curVNeighbors
		if depth[neighbor] < 0 {
			depth[neighbor] = depth[curV] + 1
			toExpand.push_back(curV)
		}
}

```

Problem C
--------
Print all anagrams of a string

```

vector<string> prefix;
set<string> ans;
void anagram(
	map<int, int> nc){

	if(nc.count() == 0)
		ans.insert(prefix);
	return;

	for(key in nc){
		nc[key] --
		prefix.push_back(key)
		anagram(nc)
		prefix.pop_back()
		nc[key]++
	}

}


```


Problem D
---------
Given an array of integers, return true if there're 3 numbers adding up to zero (repetitions are allowed)
{10, -2, -1, 3} -> true
{10, -2, 1} -> true -2 + 1 +1 =0

```
for each new index, check if current set has -a[i], go through 0 to i - 1, and add the combo to the set 
```
