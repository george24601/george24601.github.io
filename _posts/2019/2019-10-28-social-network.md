---
layout: post
title: "System design: social network"
description: ""
category: 
tags: [interview]
---

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
