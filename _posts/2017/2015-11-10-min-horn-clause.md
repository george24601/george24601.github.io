---
layout: post
title: "Code Jam 2008 1A: Milkshakes"
description: "Horn clause"
category: 
tags: [google-code-jam]
---
Unknown: arrangement

Known: clients preferences

Conditions:

    1.Each client has at most 1 malted
    2.Arrangement has min # of malted
    3.All customers must be satisfies, i.e., at least 1 in arrangement must match customer preference
 
Insights:

    1.Only 1 choice case: arrangement is forced
    2.If many choices have same set of item preferences: all 0s will do
    3.In terms of order of processing, we must solve all single cases, and propagate the changes and solve all single cases it generates
    4.What remains is list of preferences with all free arrangements, each has at least 2 entries => all 0 would work, because we know at least 1 is 0!

However, I run into serious problems implmenting pseudo code above. Namely, updating the list of user requirements and extract the remaining
one

Official solution:
------

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


Reflections:
------

The basis of the algorithm is unit propagation. But because there is at most 1 true value. We dont have to keep track of # of active
clauses => there is only 1 choice, unlike the general unit propagation case, where you need the count to help decide if it is the time to
flip. 

In either general or specialized case, we just need to know should we flip 0 to 1, and never from 1 to 0
because it is flipped by previous propagation

This unit propagation pattern is

    1.start with identity answer
    2.Similar to the spirit of greedy solution, move to the optimal solution one step at a time, with sure-fire step that improves the
result
    3. This sure-fire step becomes the inductive step
    4. if there is no more step we can apply, we know we've reached the optimal solution

This pattern can be seen in a lot of optimization problems, e.g., Bellman-Ford, greedy algorithms, gradient descent..
