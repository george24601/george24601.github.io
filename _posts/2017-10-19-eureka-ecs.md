---
layout: post
title: "Spring Cloud Eureka on ECS"
description: ""
category: 
tags: [middleware, eureka]
---
A working config for Eureka cluster deployed in AWS
```yaml
spring:
  profiles:
    active: NotDefault #to trigger the @Bean injection on aws aware context
server:
  port: 8761

eureka:
  environment: aws #shown in the Eureka UI
  client:
#I choose true for cluster case, so that cluster will keep receiving heartbeats and some false alarms will not be raised
    registerWithEureka: true 
    fetchRegistry: true
    region: us-east-1
    eurekaServerURLContext: eureka
    eurekaServerPort: 8761
    useDnsForFetchingServiceUrls: true
    eurekaServerDNSName: YOUR.DOMAIN.IN.ROUTE.53
    availabilityZones:
      us-east-2: us-east-1a, us-east-1b

  server:
    waitTimeInMsWhenSyncEmpty: 0
    #Note we need to set server.aWSAccessId and server.aWSSecretKey, so that the instance can bind to an EIP
    #Take them out for confidentiality case
    #Alternatively, you can just set environment variable eureka.server.aWSAccessId and 
    #eureka.server.aWSSecretKey
  region: us-east-1
  datacenter: cloud
  us-east-1:
    availabilityZones: us-east-1a, us-east-1b
```

Problems I run into when I pack Eureka inside docker
--------------
1. Use java -jar eureka.jar does not trigger injection at @Bean. Still don't understand why, mostly likely because of missing jar in classpath
2. Then I tried running gradle wrapper directly inside container, but the minimal java docker image does not have bash, which gradle wrapper requires
3. So I changed to base image to centos 7, but JAVA_HOME is not set, so I have to add CMD to install java and set JAVA_HOME explicitly
