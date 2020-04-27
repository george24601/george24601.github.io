---
layout: post
title: "What's the most important part of getting your job done?"
description: ""
category: 
tags: [interview, behavioral]
---

[Source](https://hire.google.com/articles/7-proven-job-interview-questions/)

### For interviewer

* Identify workflow, are you able to learn tools/methods from the candidate?
* How do they deal with problems that are bound to happen in the work
* Alternatively, "Describe your process for [a major part of the job]


### What's the most importatnt part of getting your job done

The most important part is to get stakeholders, most likely from different teams, to agree on the context of the problem and document this aggrement. Such agreement needs to be revisited once two week

This includes:

* Goals and measurements, i.e., what external impact we can have after we finish this project, this number should be meaningful to the external user/customer, and not intenal metrics within the team carrying out the task
* Roles of stakeholders: who is the DRI. who can veto, who can inform, whose (ideally no more than 3) signoffs are needed
* Non-goals: cleared rejected scopes, so that later we can revisit it
* Milestones and revisit internal: every 1-2 weeks. 

### Describe your process for handling performance problems

#### Case 1: slow query

* API timeout
* Check slow query log, identify slow queries by long running process
  * Re-run the query on prod or repro env to reproduce it, espeically take out the where checks not cover by the index to make sure it doesn't scan too many rows or bad query plan
* Informed stakeholders and applied it in 30 mins. Documented RCA the following morning

#### Case 2: timeout too long
* In preparation for the big campaign, I am inspectiving modules, one module's p99/max spikes on production
* Check db qps and latency - Normal
  * Check external API - way long than expected, and the main API didn't give 408 or 504 
  * Check the past load testing result, without this external API call all within the expectation
  * Discussed with the owner of the module, confirmed it is a tech debt
* Do nothing until the next release cycle because this one does not give customer impact

#### Case 3: connection pool size too small
* API timeout
* Check the chained API, the latency is good, and request count really low
 * Suspect client side problem. Check the HttpClient docs, find the common problem 
* Informed stakholders in a day and documented RCA.



### Describe your process for handling SEV 2 and above

#### Case 1: user didn't see the payment on 

#### Case 4: periodically latency spike


#### Case 2: Run out of auto incremental id


### Describe your process for handling disagreement with 360

#### Case 1: db selection - round 1

#### Case 2: db selection - round 2

#### Case 3: the performance impact of count(*)
