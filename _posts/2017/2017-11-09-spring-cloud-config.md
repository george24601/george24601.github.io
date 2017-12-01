---
layout: post
title: "On Spring Cloud Config"
description: ""
category: 
tags: [middleware]
---

1. Its use case is a remote application.properties file with HA. Therefore, auto refresh is possible but not natively supported, and config client will not retry if the config server returned from eureka is dead => just like how local java app reacts when the application.properties is changed on disk, or the location of the .properties file is not valid

2. It has no client/server cache => every request does a brute force git full => yet another reason when auto refresh is not a good idea => very easy to overwhelm git backend

3. Therefore, every time we deployed config change, we will have to do a rolling restart of our service

4. It is not designed to handle secretes, e.g., API key, db password, because the decrption of such information happens on the SERVER side. This means the http (not S) response client sees will have secret in plain text. Use Vault or AWS parameter store to manage secrets

5. Cloud Config can push to client via RabbitMQ or Kafka, although adding such coupling just for notification is debatable

For AWS parameter store, each account can have max 10k keys. This may not be enough for the usage of general config server.

Conclusion: spring cloud config is a lightweight tool to manage feature flags and non-sensitve configs, but not for secrets an real-time updates
