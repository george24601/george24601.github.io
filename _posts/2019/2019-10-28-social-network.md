---
layout: post
title: "System design: social network"
description: ""
category: 
tags: [interview]
---

### Relationship graph

As # of n-n relationship grows, relational and then graph model becomes more appropriate

Note that for Vs and Es, they don't need to be of homogenous type at all

* vertices:
 1. uid
 2. k-v pairs for properties
 3. set of incoming edges
 4. set of out going edges
* edges
 1. uid
 2. k-v pairs for properties
 3. marks what kind of relationship this edge represets
 4. both ends of the edge

This naturally means we can store Vs and Es into different tables/DBs

Long polling: set up a from browser to web-server HTTP connection - only used to receive push notificaiton. server side will hold it, until push notification received, our time out. May need to empty ping from time to time as heatbeat to avoid timeout

Daily system notification: display notification only first time user logs in today. Use polling, and keep track of the last polled time.

Notification to group member - 1 copy, for each member, keep a last_ack_time. Use client to dedup in case of repeated push/lost ACK problem. Note that the read ack data is not critical -  for read info, we can just delete, and archive or delete timeouted ones

### Feeds

1. Each user has a feed queue to record all feeds it sends, but read performance will be affect as feed needs to be generate on the fly.
2. Need seperate table to store user -> fan, and user -> follows, for write and read broadcast
3. Each user also stores feeds it received. 
4. Optimization: separate user into groups, only push for active users

### Likes
Need to keep track of like counts and who liked the comment

* One design
  * Store the like in reids, and persist it to db asyncly
  * Use hash to store the all likes related to a post
  * On db, store the like operation, post_id, user_id, created_at, update_at
  * The main problme with this design is the large key problem
* Design 2
  * Use bloomfilter to store posts one user liked
  * Insert into db like activity
  * Update the like cnt and store post one user liked via hyperloglog
  * Use 
