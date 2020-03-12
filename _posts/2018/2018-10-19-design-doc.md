---
layout: post
title: "How to write design doc"
description: ""
category: 
tags: [pm]
---

### Summary section

* Roles of stakeholders. Declare it explicitly for each person. Important roles includes:
  * Perform: carry out
  * Accountable: one single owner
  * Control: review the results, can veto. binding advice
  * Suggest: non-binding advice
  * Informed: should know the result of activity. Do NOT have a say in how work is done 
* Overview
  * Read by all engineers to decide if they need to keep reading
  * 3 paragraphs max

### Context section

Different teams should mostly interact in this section instead of the decision section. So that teams can be highly aligned on goals

* Motivation 
  * Non-dev should be able to read it
  * Show how the project is tied to the long-term goals
* Assumptions
* Goals 
  * in the dimension of user-driven impact
  * measuable key results
* Non-goals

### Decision

Mostly for intra-team discussions.

* Milestones
  * For PM and manager's manager to quick read
  * Set up milestones and check points. Ideally, milestones should enable partial launch of the project
* Current/Proposed solution
  * Probably need to include user stories (from different povs) to show how the solution behaves dynamically
  * De-risk the project ASAP, by focusing on the risk part and prototyping and scaffolding
  * Probably need to prototype before you are able to propose
  * Details go here. Use user stories to show how the system behaves
  * Include precise pseudo code and data structure
  * Someone didn't write the doc but understands the problem should be able to implement the solution 
* Alternative and Rejected Solutions
* Monitoring and Alerting
  * Probably include correctness invariant and testing strategy. Testing strategy needs to be detailed too
* Cross-Team Impact
* Discussion
  * If the discussion has more than 5 comments, in-person discussion is probably better
* Detailed Scoping and Timeline
  * By engineers, tech lead, and managers, that is why it is near the end of the doc,i.e., end user is the consumer of the design docs without the scoping part 
  * Update the scope throughput the project. Most likely you need to update your estimate after each milestone 
  * Each task step should take 2-3 days max
  * 1.5x of your estimated dev time to include buffer for interrupt tasks
  * Timebox open-ended questions and commit to a solution within the box, but be aware of trade-off between time box and long term cost 
  * Dropping the project or stop it right after certain milestone is an option too. 

## A example template
* Context and motivation 
* Assumptions and prerequisites
* Goals
  * in the dimension of user-driven impact
  * measuable key results
* Non-goals
* Role assignment for each member, use one of the following role:
  * Perform: carry out
  * Accountable: one single owner
  * Control: review the results, can veto. binding advice
  * Suggest: non-binding advice
  * Informed: should know the result of activity. Do NOT have a say in how work is done 
* Current/Proposed solution
* Alternative/Rejected Solutions
* Cross-Team Impact
* Monitoring and Alerting
* Milestones/Timelines

### References
* [Design doc](https://medium.freecodecamp.org/how-to-write-a-good-software-design-document-66fcf019569c)
* [Scope](https://medium.freecodecamp.org/how-to-effectively-scope-your-software-projects-from-planning-to-execution-e96cbcac54b9)
* [Netflix's culture](https://jobs.netflix.com/culture)
