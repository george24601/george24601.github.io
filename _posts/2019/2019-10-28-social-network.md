---
layout: post
title: "System design: social network"
description: ""
category: 
tags: [interview]
---

### Data structure: relationship graph

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

### Requirement: Daily system notification

Display notification only first time user logs in today.

Design:

* Maintain a list of notifications. Since this is admin message, the volume will be no more than < 1k per day. So HA DB with read replicas is enough
  * We can even cache the most 10 days notification in memory, as long as it is immutable
  * Due to epoll, each server can hold at least 20k connection per server, for 200 mil users, this means 10k servers is more than enough to handle the peak
* Client side, keep track of last login time, if the date is different, ask server side to compute the list of notification to push
* Server side, use rules to compute notification
 1. created since the last poll 
 2. within the scope of the notification's target
 3. may from the most recent to fetch only top k
 4. update the last poll time after push notification is successful


### Requirement: Unread group chat for group member

Design:

* Maintain a group message table (group_id, message seq or message_created_at) -> message_id 
  * Asssuming each user is rate limited to 5 sec between messages, so write is only 200 for a 1k man group
* Maintain a user group membership table (user_id, group_id) -> last_pollled_time 
* Client side polls the group membership table with locally saved last poll time. Note that client side may needs to dedup message in case of retries 

### Requirement: can publish feed and read feeds

Also supports the pagination on the feeds page

Design:

* May use seperate table to store user -> following and user -> follower relationships for write and read broadcast
 * the data structure will be (user_id, following_id) -> metadata
 * May also need a table to do include (user_id) -> follower_count/following_count 

Do we need to persist read feed on publish? 
* Need a background job to populate to all followers. Latency concern to see popular user's post
* Write amplification - imagine the celebrity, write amplies to tens of millions
* So read feed on publish is not feasible
* What if i want to see the historical posts of a person i just followed in my feed? This means every time i follow and un-follow, I need to mark the current persisted feed as outdated and need to populate the delta

Can we generate read feed on read?
Possible for the first page
* Get the top K most recent updated posters by scatter-gather or a join
* Get the top K posts from each of them and then sort max k^2 posts in memory
* BUT, if we include pagination into the picture, scrolling means true scatter-gather, may cause read amplification problem. So generate feed on read may not be feasible if we are strict on the pagination semantics

How about generating persisted feed on demand?
* This is possible. Consider 100 mil MAU, each person reads 200 feeds per day, this means we need to store 20B records per month, each record takes 128 bytes would mean 3PB, note entirely unreasonable
* Again, this means we need to keep track of deltas of the user's recently added/removed poster, so we can update the feed

### Requirement: User can like a post and undo it

We need to display
1. like counts
2. How many among your friends liked the count

Design:
* maintain a (post_id, user_id) -> metadata table
* post_id -> liked_cnt table is debatable. Most likely we need it. We can also use a hyperloglog data structure, which has 2% error with 1.5KB of memory with cnt > 1e9
* similarly, we can use a post_id -> liked user_id bloomfilter table to speed up scatter-gather. Less than 10 bits for 1% false positive rate 
* Pre-compute the liked count or not is very similar to the post feed discussion. Each page we see no more than 10 new posts, so compute in memory is good enough
