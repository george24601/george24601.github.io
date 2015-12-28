---
layout: post
title: "Akka throttler example - from official Akka pattern page"
description: "Example of FSM"
category: 
tags: [akka]
---

Problem Statement
--------

1. Send request to a target, throttled on the client side, by a rate of message per interval

2. if over the threadsold, the message will be sent in the next interval

3. It is possible that request might be clustered around the boundary of intervals, for the sake of excercise, we accept that limitation, i.e., client side peak usage may be twice as many as our throttle

4. The internal timer should be turned off/go idle when we end an interval with no remaining message to relay, so that to save resources


Design
-----

1. throttler will have 2 states, idle and active

2. when go from idle to active, we will set timers to send ticks to actor, each tick represents a interval. Conversely, when we go from
active to idel, we shutdown the timer as per statement NO.4

3. The throttler FSM state will keep track of: 

    1. message queue
    
    2. voucher count, i.e., how many messages can be sent in this interval
  
    3. send target, represented as an actor.

Note that we put these into FSM state, because upon receiving a new message, we need to swtich ,i.e., pattern match, on a combiniton of
these states. Note the difference between internal state and FSM state here, we used both terms here!

4. For the interface of throttler, it needs to accept new target from outside, and new message. Each use case means a type of message

5. When throttler is idle, i.e., the starting state.

    if get a new target message, stay idle, update the internal state

    if get a new delivery message, switch to active state, with the concrete try to flush call as part of transition

6. When throtterl is active
  
    if get a new target message, stay active, update the internal state, try to flush as part of transiton
  
    if get a new delivery message, stay active,try to flush as part of transition 

    if get a tick, while buffer is not empty, stay active, try to flush, update the voucher counter

    if get a tick, while buffer is empty, switch to idle

7. Implementation of flush just to update voucher counte and message queue, and do the actual tell

8. to test, use TestKit echo actor to wrap around and use expectMsg/expectNoMsg within time period
    


