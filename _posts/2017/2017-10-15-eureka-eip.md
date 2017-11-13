---
layout: post
title: "Spring Cloud Eureka Source Code: How EIP is bound via DNS"
description: ""
category: 
tags: [middleware, eureka]
---
1. Spring-Cloud-Eureka-Server has its now ServerBootStrap. This means the bootstrap logic from vanilla Eureka-Server will be ignored
2. Spring-Cloud by default sets the binding strategy to EIP, i.e., we will be using EIPManager from vanilla Eurika
3. To use DNS resolivng, we need to set shouldUseDnsForFetchingServiceUrls to true in client config. 
4. The DNS name we need to config on the client side is 
```java
clientConfig.getEurekaServerDNSName()
```
5. EIPManager look for the record "txt.YOUR_REGION.SERVICE_DNS_NAME" of TXT type, and get a list of CNAMEs from the value of TXT field. Note it expect the first part of CNAME is yoru zone name
6. For each CNAME record, EIPManager will look up "txt."CNAME TXT field and another list of CNAMEs, and construct service URL in the form of 
```java
 String serviceUrl = "http://" + ec2Url + ":"
                            + clientConfig.getEurekaServerPort()
                            + "/" + clientConfig.getEurekaServerURLContext()
                            + "/";
```
Note EurekaServerURLContext - we need to config it in client config
5. To trasnlate from service url in DNS to EIP, note it is brute force string parsing - get substring between "ec2-" and ".yourregion.compute", and then replace all - in the substring with .
6. To bind EIP, we need to configure 
```java        
String aWSAccessId = serverConfig.getAWSAccessId();
String aWSSecretKey = serverConfig.getAWSSecretKey();
```
in service config
7. Note that EIP is a public IPv4 address, but we really want to shield eureka from internet traffic. Instead, we can put eureka instances inside the private subnet, while still using the public host name. The communication from within the VPC should still work, because AWS resolves public DNS host name to the private IPv4 address

