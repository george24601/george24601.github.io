---
layout: post
title: "Kelly criterion & Zipf's law"
description: ""
category: 
tags: [interview]
---

### Kelly criterion

A more general problem relevant for investment decisions is the following:

1. The probability of success is  p.
2. If you succeed, the value of your investment increases from  1 to 1+b.
3. If you fail (for which the probability is q=1-p) the value of your investment decreases from 1 to 1-a. (Note that the previous description above assumes that  a is 1).

The percentage to bet is `p/a - q/b`

### Zipf's law

The frequency of any word is inversely proportional to its rank in the frequency table. Thus the most frequent word will occur approximately twice as often as the second most frequent word, three times as often as the third most frequent word, etc.

Only 135 vocabulary items are needed to account for half the Brown Corpus. Note that this is somewhat related to parent to Parento priciple, i.e., the 20/80 rule.

The same relationship occurs in many other rankings unrelated to language, such as the population ranks of cities in various countries, corporation sizes, income rankings, ranks of number of people watching the same TV channel

Zipf's law is most easily observed by plotting the data on a log-log graph, with the axes being log (rank order) and log (frequency)

#### References
* [Kelly criterion on Wikipedia](https://en.wikipedia.org/wiki/Kelly_criterion)
* [Zipf's law on Wikipedia](https://en.wikipedia.org/wiki/Kelly_criterion)
