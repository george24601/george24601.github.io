---
layout: post
title: "Interview Problems from careerCup"
description: ""
category: 
tags: [interview]
---

Problem A
--------
Given a positive integer n, find the no of integers less than equal to n, whose binary representation doesn't contain consecutive 1s.
eg:
I/P : 4
O/P: 4 (0,1,2,4 Valid)


Idea
-------
If a number has consecutive 1s, then it is either 

1. number of consecutive 1s, with last bits 0

2. (last bit 1 case) number of consecutive 1s, with last bits 01

3. (last bit 1 case) any number, with last bits 11 

Note that by master theorem, this solution is only slightly better than linear. Because similar to the tree traversal case, even though the combination steps is minimal, the relative large # of subproblems cancels the reduction in problem size

Solution
---------

```
int c11(int n){
	if (n < 3) 
		return 0
	int ans  = c11(n/2);

	if (n % 4) == 3 {
		ans += c11(n/4)
	}else{
		ans += c11(n/4 - 1)
	}


	if (n % 4) > 2 {
		ans += c11(n/4)
	}else{
		ans += c11(n/4 - 1)
	}

	return ans;
}
```



Problem B
--------
Giving a method intToEnglish that receives an int as a parameter, how do you return its representation in english words. The number can be of any size but no more than around 2 billion since the parameter is an int 2ˆ32

Idea
--------
1. need to get rid of billion, million, thousand suffix 
2. need to get rid of hundred number
3. need to get rid of tens number


```
string ltle2(int n) {
	string ans = ""
	
	if (n / 10) {
		//return the value based on n % 10
	}

	ans += //return value based on n % 10
}


string lt1e3 (int n) {
	string ans = "";
	if (n / 100){
		ans += lt1e2(n% 100)
		ans += "Hundred"
	}

	ans += lt1e2(n / 100)
}

string intToEnglish(int n){

if (n == 0) {
return "Zero"
}

int vs[3] = [1e9,1e6, 1e3, 1] 
int ns[3] = .....

string ans = "";
	LP(i, 0, 3) {
		if (n / vs[i]) {
			ans += lt1e3(n %vs[i])
			ans += ns[i]
			n %=  vs[i]
		}
	}
}

```
