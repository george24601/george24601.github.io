---
layout: post
title: "Interview Problems from careerCup"
description: ""
category: 
tags: [interview]
---

Problem A
--------
Find the median of unsorted array
Have to do better than O(nlogn) time.
e.g.
Given [2, 6, 1] return 2
Given [2, 6, 1, 4] return 3 which is sum of the two elements in middle over 2


Idea
-------
use the linear random selection algorithm


Code
-------

int median(vector<int> a){

	int firstM = find(a, 0, a.size(), a.size()/2 + 1);

	if(a.size() % 2){
		int secondM = find(a, 0, a.size(), a.size()/2 + 2);
		return (firstM + secondM) / 2;
	}else{
		return firstM;
	}
}

int find(int si, int ei, int rank){

	if(si == ei)
		return si;

	int pivot = randmon(si, ei)
	swap(si, pivot)

	int bandEnd = si + 1;

	LPE(i, si + 1, ei) {
		if(a[i] <= a[pivot]){
			swap(bandEnd, i);
			bandEnd++;
		}
	}

	int bandLen = bandEnd - si;

	if(bandLen <= rank)
		return find(si, bandEnd - 1, rank);
	else
		return find(bandEnd, ei, rank - bandLen);
}
		


Problem B
--------
A "derangement" of a sequence is a permutation where no element appears in its original position. For example ECABD is a derangement of ABCDE, given a string, may contain duplicate char, please out put all the derangement
public List<char[]> getDerangement(char[]){}


Idea
-----
Sure we can brute force to generate all permutations and then filter, but it will be too slow, so we will have to filter it early. i.e., we should generate the number index by index

set<string> solve(){
	vector<int> cnt(26);
	set<string> toReturn;
	//add each char to the cnt map
	derangement("", 0, cnt, toReturn);

	return toReturn;
}

void derangement(string prefix, int i, vector<int> cnt, vector<string> toReturn){

	if(i == s.length())
	{
		toReturn.insert(prefix);
		return;
	}

	for(int i in [0, 26))
		if(cnt[i]){
			char nextC = 'a' + i;
			if(s[i] == nextC){

			}else{
				cnt[i] --;
				prefix.push_back(nextC);
				derangement(prefix, i + 1, cnt);
				prefix.pop();
				cnt[i]++;
			}
		}

} 

