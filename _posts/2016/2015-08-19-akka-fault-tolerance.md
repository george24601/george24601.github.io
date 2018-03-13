---
layout: post
title: "Akka fault tolerance example explained" 
description: ""
category: 
tags: [akka]
---
See "Fault Tolerance Example" in Akka doc for details.  

### Scenario ###
        * Worker does simple work, in this case just counting
        * We want to know the progress from worker, which needs to be in storage
        * Storage may fail. We will restart it and reconnect. Such failure should not be localized and not reach the top-level actors.

### Design actors ###
        * Obvious ones: Worker, Storage, and a Listener to report progress
        * According to error kernel pattern, we need a supervisor for storage => CounterService
        * To make actors simple to reason, CounterService, as supervisor, should be as simple as possible. Therefore, we introduce a Counter actor as proxy, so that Storage handles only k-v update, while Counter decides exactly which operation applied to Storage. CounterService also receives requests from worker. Counter handles in state update in memory, but checkpoints to Storage

### Detailed design: Listener ###
	* As per the design goal, Storage error should not surface to Listener, so it needs only to handle standard ReceiveTimeOut, just call context.system.shutdown
	* Outer sources will send progress info to Listener, since Listener is keeping track of Worker, Worker send the message to Listener, which means Worker has Listener ActorRef

### Detailed design: Worker ###
	* Worker needs Listener reference, we can pass Listener to Worker as an init message with its ActorRef as pay load
	* On receiving doWork message, send a update progress message to CounterService, and get updated progress from CounterService. The get call will return a future, we will map the future into desired type and pipe it to the listener
	* Worker will have its own CounterService since we can afford to, similar to the crawler example, and Worker will stop CounterService upon getting ServiceUnavailable

### Detailed design: Storage ###
	* Just a simple wrapper, the Get call may pipe the future to sender. Although need to keep sender reference safe

### Detailed design: Counter ###
	* since the Storage just wrapps K-V store, the actual increment logic and corresponding state has to be in Counter
	* the ActorRef of Storage will come in as part of init message 
	* If there is error, we will leave recovery to Storage's parent: CounterService

### Detailed design: CounterService ###
	* CounterService will be the parent and Storage, since it is supervisor, it should DeathWatch the Storage, so that it can have custom logic to react to Storage's end 
	* The general supervisor rule should affect Storage and be restart. After final restart fails, CounterService will receive Terminated message. When CS sees Terminated message, we need to tell Counter to stop using Storage, and tell CS itself to reconnect, which just initStorage
	* initStorage should be part of preStart(), since we need init Storage when we first start anyway. Moreover, it should create new Storage actor, and tell Counter to use the new Storage
	* For normal logics, if we know Storage is alive, we just forward to Storage. Otherwise, we put it on a buffer queue with max size
	* When we get ACK from Storage, we will start Counter and pass the now-ready Storage over
	

