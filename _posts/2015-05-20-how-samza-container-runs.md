---
layout: post
title: "Samza task container: a quick overview"
description: ""
category: 
tags: [Samza]
---

Each container is single-threaded. This means container will multiplex messages
and functions to task instances inside the container

### What in the run() loop ? ###

1. Choose one envelope from internal message chooser

2. Update message chooser with an envelope from the supplied stream partition 

3. Take the head envelope off the message queue for that stream partition

4. Update the message chooser

5. Based on the stream partition, get a task instance

6. Task instance will process this envelope,i.e., call process() of your StreamTask

7. Window function operation

8. Samza commit logic 


### How does container get messages to process? ###

Container refreshes message queues periodically, or if there is no message to process is empty. Notice the check is done inside the same thread, since task instance itself is single-threaded

Every time container refreshes, it will try to poll new data from underlying consumer. 

For Kafka, the underlying consumer is implemented in an asynchronous way, i.e., Kafka consumer uses BlockingQueue as buffer, which will answer poll requeset from task container. Kafka consumer also spawns one thread for each stream partition as worker threads to fill the buffer
