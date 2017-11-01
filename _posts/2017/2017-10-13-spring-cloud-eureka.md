---
layout: post
title: "Config Related Code in Spring Cloud Netflix - Eureka"
description: ""
category: 
tags: [middleware, eureka]
---

Server Dependency
-----------
Your Eureka project -> spring-cloud-starter-eureka-server -> spring-cloud-starter-netflix-eureka-server, which depends on

1. spring-cloud-starter
2. spring-cloud-netflix-eureka-server
3. spring-cloud-starter-netflix-archaius
4. spring-cloud-starter-netflix-ribbon
5. com.netflix.ribbon.ribbon-eureka 

Note that there is no concrete code until spring-cloud-netflix-eureka-server

During Server Bootstrap
---------
1. Look for spring.profiles.active
2. Look for spring.profiles.default
3. Look for eureka.datacenter
4. Look for eureka.environment
5. Check if applicationInfoManager's dataCenterInfo is AWS/Amazon, if so, start the AWS binder. Note that it defaults to EIP binding strategy 
6. For other settings, check ServerConfigBean, which is populated via reflection/injection. AVOID VANILLA EUREKA CONFIG PATH FROM NETFLIX DOC!

Spring Cloud Eureka Client Config
--------
1. Client will look for az in a region, if az is not defined, it will use "defaultZone"
2. Client will look for serviceUrl."myZone", if it is undefined, it will look for serviceUrl.defaultZone
3. serviceUrl defaults to "defaultZone" -> "http://localhost:8761/eureka". That is why local run can do without setting default Url
4. For other settings, check ClientConfigBean, which is populated via relfection/injection. AVOID VANILLA EUREKA CONFIG PATH FROM NETFLIX DOC!

Note
--------
1. In my Eureka project, application.yml and application.properties can co-exist in src/main/resources and both will be read. I use application.properties to config logger, which [is logback](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-logging.html)
2. Following the guide, the aws specific init is placed in the main of my eureka server. Note the example has a @Profile("!default") flag, i.e., you need to set spring.profiles.active to something other than "default", either via -D arguments or application.properties
