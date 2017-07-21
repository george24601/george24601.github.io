---
layout: post
title: "Interview Problems from careerCup"
description: ""
category: 
tags: [interview]
---

Problem A
--------
```
There are N gas stations along a circular route, where the amount of gas at station i is gas[i].
You have a car with an unlimited gas tank and it costs cost[i] of gas to travel from station i to its next station (i+1). You can begin the journey with an empty tank at one of the gas stations.
Return ALL the starting gas station's index where you can travel around the circuit once
public List<Integer> canCompleteCircuit(int[] gas, int[] cost) {
}
```

Idea
---------
Need prefix sum of roads and stations for sure. At each station, it is easy to calculate what is the cost and gain so far, and we will break early if cost > gain. O(n^2)

To get run time to O(n), we need to break our cycle into 2 parts =>  from current to end + from 0 to curret,  and answer question for each gas station i

```
1. what is the minimum amount of gas at station 0 we need to reach gas station i?

2. what is the minimum amount of gas at station i we need to reach gas station 0? Part 2 can be calculated by reversing all indices, and run the procedure we need to calculate the question above

```
Then we iterate through all stations, and it is feasible if gas[i] >=q2[i] && gas[i] + netcost to go from i to 0 >= q1[i]

I have to admit, coming up with O(n) solution and code it in less than 35 mins would be really challenging in a real interview!



Problem B
--------
```
Given a task sequence tasks such as ABBABBC, and an integer k, which is the cool down time between two same tasks. Assume the execution for each individual task is 1 unit.
rearrange the task sequence such that the execution time is minimal.
Example:
S = AAABBBCCC, K = 3
Result: ABCABCABC (all 'A's are 3 distance apart, similarly with B's and C's), thus return 9
S = AAABC, K=2
Result: ABCA_ _A, thus return 7
S = AAADBBCC, K = 2:
Result: ABCABCDA, thus return 8
public int rearrangeTasks(String tasks, int cooldown){
}
```

Idea
--------
Brute force: generate all permutations, and calculate cost in each case

Greedy Idea: 
For the next entry, we take one with minimal (cost, -numberOfEntries). However, cost = max(0, cooldown - i - lastUsed), this means we will have to iterate through all task types and calculate which one to use. The answer we get is the optimal soltuion that triggers costs in a lexically greatest way

We can also try filling tasks in a radix way, but handling gaps proves to be tricky 


Problem C
--------
Input: A string equation that contains numbers, '+' and '*'
Output: Result as int.

For example:
Input: 3*5+8 (as String)
Output: 23 (as int)

```
int nextI;

int solve(string s){
	int sumSoFar = 0;
	int lastNum = parseInt()
	
	while (nextI < s.size()){
		char op = parseOp
		
		n = parseInt()
		
		if(op == '+'){
			sumSoFar += lastNum
			lastNum = n;
		}else{
			lastNum *= n
		}
	}

	return sumSoFar + lastNum
}
```


