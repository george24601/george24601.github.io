---
layout: post
title: "System design: distribute whitelist"
description: ""
category: 
tags: [systemDesign]
---
Consider we have mail client that updates the blocked website list for our service. Design one such service so that we can distribute the black list.
the server list is updated once per second
the client pulls our service once every 30 mins

Design:
Obviously, we need to optimize for read performance

so the client has been updated 1800 times between each pull

The client should send a request fetchUpdate(clientVersion), which should return the deltas since the client version

On the reader side, we should have a list of deltas and with each version, and return list of versions. In a k-v store that supports range scan, this should be highly efficient => because they are aligned together in persistant layer

To future improve performance for long missed read, we can have a compacted view every certain versions,
if the new request < last compacted version, just return the whole thing. we should do a log compaction from time to time to purge too old deltas.

In terms of global updates, we will designate 1 DC as master, and other as slaves, and we use cross region ZK to locate which one is alive and forward all write traffic to it. when the master recovered, we will just talk to the new master and sync from the single source of truth to recover
