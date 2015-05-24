---
layout: post
title: "Understanding Samza metrics"
description: ""
category: 
tags: [Samza, Kafka]
---

### Common setup: ###
Each container periodically publish to "metrics" topic, then another job/consumer to demultiplex and consume these metrics. Note that each task instance will write a separate message

Notice counter-types metrics are number counted between the reset-time field and time field in the header

### SystemConsumersMetrics ###

This is metrics for SystemConsumers class.

	kafka-polls: increased by 1 for each poll request sent to system consumers, each of which maps to a topic partition. Since this is the number of poll requests it should be less than 20/sec

	kafka-ssp-fetches-per-poll: when we detect mpty topic parittion buffered on our consumer, we try to fetch system consumers. This number is increased by the size of fetch set
	
	kafka-messages-per-poll: after kafka-ssp-fetches-per-poll, we poll the fetch set and incoming message envelopes. Note this is the number of polling fetch set, NOT number of incoming envelopes

	unprocessed-messages: after kafka-messages-per-poll step, increased by the size of each envelop

	chose-null: increased by 1 when chooser is empty,i.e.,  no new message

	chose-object: increased by 1 when we get an envelope from chooser,i.e., dual of chose-null

	kafka-{topic}-messages-chosen: increased same time as chose-object, but we demultiplex count into topic the envelope is from


### SamzaContainerMetrics ###

Used by container main runLoop. Each container is single-threaded, activities for all task instances within a container are multiplexed onto one execution thread

	process-call: increased by 1 when we attempt to choose a message to process

	choose-ms: updated when get an envelope from consumerMultplexer

	process-null-envelopes: increased by 1 if the envelope we get is null

	process-envelopes: increased by 1 if the envelope we get is NOT null

	commit-ms: updated for the whole commit action

	commit-calls: increased by 1 when
		"Committing task instances because the commit interval has elapsed."
		"Committing due to explicit commit request."		


### TaskInstanceMetrics ###
	sent-calls/messages-sent: # of send() calls

	flush-calls: # of flush() calls
	
	process-calls: # of process() in your task

	window-calls: # of window()

	commit-calls: # of commit()

	kafka-{topic}-{partition}-offset: when register consumer for target,  update this to the last processed offset of that partition
	
