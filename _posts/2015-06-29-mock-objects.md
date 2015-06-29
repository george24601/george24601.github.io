---
layout: post
title: "Mock Roles, Not Objects" 
description: "Reading notes on the paper of the same name"
category: 
tags: [testing]
---

1. mock is a design technique to help figure out object behavior,i.e., who are the neighbors i talk to, what messages we send? Being a design technique means:

It is used at design time, not testing time,i.e., BDD/TDD

It is not a silver bullet or rule, and can only supplement but not supercede general problem-solving techniques

2. Mock operates in a more top-down way: you have a know role as the starting point, and you assume the collaborators exist. Mock objects gives you surrogate collaborators

From problem-solving perspective, this is the classic application of inventing auxillary problems by swapping part of unknown and known.

Original problem:

Known: overall role 

Condition: role must perform some spec

Unknown: whole system implementation


Auxiliary problem

Known: overall role and some collborators

Condition: role must perform some spec

Unknown: collaborator implementation


3. But what if I need to design things in a bottom up way, as common in distributed systems?

Then we are almost at the place of the auxiliary problem, except unknow is overall role implementation instead of collaborator implementation.

Option 1: solve to the problem as is. The risk is overall role may not give good encapsulation and abstraction

Option 2: swap part of unknow and known again, i.e., force collaborator to implement in a certain way to force the top-down view of overall role

4. A common anti-pattern is mocking a type you do not own, because their behavior often changes and is beyond your control. One solution is to create a light weight wrapper around external lib/types. However, if your wrapper just forwards data without transforming input/output, then the wrapper and the mock of it has little sense to exist,i.e., the abstraction is redundant. You should use normal integration testing
