---
layout: post
title: "Secretary Problem"
description: ""
category: 
tags: [interview]
---

The purpose here is not to show that this strategy is perfect. It just shows the inherent complexity in hiring. Whatever schemes you devise is just luck in filtering out "maybe-no"s. However, the bar happens to be much higher than what your actual job needs in many places. Thus, whatever hazing ritual shows good result, and we have to accept that as part of life and play by the rule.

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

