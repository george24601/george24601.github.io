---
layout: post
title: "Migrating from Eureka to Consul" 
description: ""
category: 
tags: [middleware]
---

Why migration
------------

1. Spring Cloud Config does not provide good secret management solutions, compared to vault + consul

2. Using Eureka implicitly infers that all our services will be using spring cloud. This assumption is too strong

3. The self-preservation node makes our DR drills trickier.

4. Modifying the main DNS entries with EIP is annoying for our DR drills. If we use hosted zones instead, the from time to time the service will report unable to resolve the main eureka DNS entries.

5. EIP is public ip, but most likely you want your Eurkea instances to be on private subnet

6. Need to explicitly override EurekaIp if you want to run your services in ECS, otherwise, it will register as the docker container ID


Migration Plan
---------
1. For each service to migrate, start a new branch with dependency and config changes. This branch keeps rebasing from dev

2. Migrate Cloud Config as the first service, so that other services can reuse it.

3. Update all services build jobs, and create a new ECS cluster that mirrors the existing ones 

4. On production, change the DNS record to point to the new ECS cluster's LB

5. During the process, keeps rebasing from the master

6. Run DR drills before completely replaces Eureka

