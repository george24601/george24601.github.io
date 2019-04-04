---
layout: post
title: "Data on the Outside versus Data on the Inside"
description: "Reading notes"
category: 
tags: [paper]
---

Classic paper on the data format/semantics related to data infrastructure

Services should act independently, i.e., they can still maintain availability even if the depending service failed. Also, there is no 2PC
between services, all transactional semantics should remain within the SAME service

However, this means that the message can either representing past or future, but just not right now, time is unreliable, and it is up to the
up to the application logic to reconcile this!

Comment
--------
```
1. Microservices means PA systems

2. Message may be stale, this means consumer of that message has the final say of the result given its context, even the result is obsolete,
or the entity no longer exists!

```

As for the message between services, we need to make sure they are stable, i.e., gives same meaning when interpreted at different
time/locations. 


Comment
-------
This suggests that

```
1. To simulate time, the ID and/or version number/timestamp should at least, not decreasing, this also means the IDs should not be reused,
because we can not move back our clock!

2. From the domain model perspective, only with ID is no longer enough for entity, we need version/timestamp as well as part of messages

3. being stable/versioned along with ID, means that the message could become indentifiable => this enables possibility for exactly-once
processing!

```

Reference data has a central authority service that publishes for other servies to use -> ID + mutliple version, itself can handle multiple
concurrent updaters

For data ingestion, we should shred the incoming message into the relational information, and keep the extra unshredded somewhere else.
Often, this original message is kept regardless for auditing and non-repudiation 

Note that schema in general is against the idea of encapsulation => that belongs to the realm of object, which itself is not-queriable at
all

