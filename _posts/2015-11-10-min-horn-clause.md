---
layout: post
title: "Code Jam 2008 1A: Milkshakes"
description: ""
category: 
tags: [how-to-solve-it, google-code-jam]
---
unknown: arrangement

known: clients preferences

Conditions:

    1. each client has at most 1 malted
    2. arrangement has min # of malted
    3. all customers must be satisfies, i.e., at least 1 in arrangement must match customer preference
 
Insights:

    1. only 1 choice case: arrangement is forced
    2. if many choices have same set of item preferences: all 0s will do
    3. in terms of order of processing, we must solve all single cases, and propagate the changes and solve all single cases it generates
    4. what remains is list of preferences with all free arrangements, each has at least 2 entries => all 0 would work, because we know at least 1 is 0!

However, I run into serious problems implmenting pseudo code above. Namely, updating the list of user requirements and extract the remaining
one

------

Official solution:

    1.start with all 0s
    2.If all clients satified, done
    3.If not satisifed, should we satisfiy its 0 requirement or 1 requirment?
    4.0 requiremnet is not satisfied because all its positions are 1 already, this means we have to set the remaining pos 1 from 0 to meet the 1
requirement
    5.1 requirement is not satfisfied, because that pos is 0. Continue with argument from the previous step, we set that bit to 1. 
    6. repeat 2 - 5


This approach works because we turn only from 0 to 1, and not the other direction. Therefore, 2-5 will run at most # of batches times, each
time iterate all customer once!

Can use a bitset to make comparison easier, also, keep malted and unmalted in seperate collections.
