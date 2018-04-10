---
layout: post
title: "Authorization and Role Management in Microservices" 
description: ""
category: 
tags: [architecture, security]
---

The discussions I found give VERY diverted opinions on how to implement authorization in a microservice environment. I will just list my findings and conclusions here

0. Most authorization rules I found in a microservice environments are role-based instead of ACL-based. May consider a specific user role/identity service to handle the user-role mapping. However, must be careful because this user-role mapping service can quickly become the kitchen skin of contexts from different domains

1. Often, a service has a very domain specifc authorization rules and roles. In this case, such logic and roles, should remain within the service, but the trade off means we need to replicate main user database, or at least access a centralized user-role service

2. Most likely you will have some global default roles, e.g., admin, billing admin. These roles should stay with the general global auth service, i.e., when we generate the JWT, we should attach the applicable global roles to the JWT. Moreover, these admin roles should be the only roles that can bootstrap service-specific roles and assign them to the users. 

3. JWT should have only global role info, because: 
	
	1. JWT is normally part of header, which is limited to 8KB. 

	2. We don't want to leak other service's context. 

4. This setup suggests that role popluation happens twice, once at the auth service, once at the actual request processing service, and the actual role/permission check is decentralized instead of centralized. 

5. Favor coarse-grained roles, modelled on how organization works




