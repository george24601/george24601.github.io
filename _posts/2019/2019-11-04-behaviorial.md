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

* Unable to reproduce in other environments. Direct affecting 200 txns per sec
* Add metrics at every single level, and release it to narrow down the code
  * Read source code and draw graph to understand the flow
* Find the explaination and mitigation, released the degraded but correct exp in 3 hours
  * Complete fix took two weeks to release, because will not experience such traffic soon, and people are tired after the big campaign


#### Case 2: periodically latency spike

* A critical service's API latency spikes every day, and k8s health check failed
* Check db query to find longest total time queries
  * Find that it is from a db co-hosted on the same server  
* Provide mitigation by turning off the cron job
  * Complete fix provided in two weeks with proper db seperation and index

#### Case 3: Run out of auto incremental id

* 36 hours before switching online, unable to execute txn
* Ask support for mitigation and test case. Tested independently both us and 3rd party
 * Contact sign off people to explain my proposal and the risk of doing so. Got approval to go ahead as planned
* Goes as planned without incident
  * Hot fix released in 3 month. Upgraded in 5 month


### Describe your process for handling disagreement with 360

#### Case 1 & 2: db selection - round 1 & 2

* Run into performance problem with existing db
* Document all pros and cons for discussion with stakeholders
  * Got overruled. Stayed my objection and propose a PoC
  * Decision made without consulting me
  * Provide migration plan to commit the success
* Run into performance problem again after 6 months. Unable to handle it even upgrade to the highest spec
  * List different options with new context
  * PoCed wtih all options
  * Stakeholder picked the option and went ahead
* Stable, incident-free deployment
 

#### Case 3: the performance impact of count(*)

* 500 error due to failed health checks during load testing
* Explained why current solution won't work. Offered sample solution. The other team's owner doesn't commit to fix it.
* Raised to my chain of command, forwarded solution, and told not to worry about it after that
