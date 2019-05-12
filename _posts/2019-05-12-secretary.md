---
layout: post
title: "Secretary Problem"
description: ""
category: 
tags: [interview]
---


### When N is know
Note its difference between real life

1. If you reject, the candidate will not appear again
2. Decision must be made on the spot
3. the interview will rank all applicants against each other

If N is known, and we are filling only 1 position

* The optimal strategy is to reject first n/e (about 37%) of candidates, and then pick the first candidate "better" than all previous ones
* The prob of picking the "best" candidate converages to 1/e (37%) too. Such result shows the inherent complexity of hiring
* An variant exists for picking k out of a pool of n, the cut-off threashold is about 0.25


### When N is unknown, 1/e strategy 

But timeframe T is known, and candiates appears in the same (not necessarily uniform) distribution

* Then we pick the the moment where the combined possibily that the best candiate appeared so far is 1/e
* 1/e chance you will get the best candidate, and 1/e chance you pick no one AT ALL
