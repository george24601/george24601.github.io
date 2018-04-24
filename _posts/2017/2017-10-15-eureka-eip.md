---
layout: post
title: "Spring Cloud Eureka on ECS"
description: ""
category: 
tags: [middleware, eureka]
---

How EIP is bound via DNS
-----------
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


###On ECS###

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

4. Need to set ip and host name explicitly, during server bootstrap. Otherwise, the host will use the internal ip host name instead of public ip host name. Such discrepancy will cause Eureka to report replicas as unavailable, because the registered host name is different from the EIP host name stored in route 53 


When Eureka instance is on ECS, it will be using the docker ID as host name to register. Therefore, the service consumer will not be able to resolve it

We will have to inject an EurekaInstance ourselves, and manually set host name and port to be registered on Eureka. Note that EurekaInstance exists in client

```java
    @Bean
    @Profile("!default")
    public EurekaInstanceConfigBean eurekaInstanceConfigBean(InetUtils utils) {
        final int  managementPort = YOUR_SERVICE_PORT;

        log.info("Setting AmazonInfo on EurekaInstanceConfigBean");
        final EurekaInstanceConfigBean instance = new EurekaInstanceConfigBean(utils) {

	   //needed only when Eureka server instance binds to EIP
            @Scheduled(initialDelay = 10000L, fixedRate = 30000L)
            public void refreshInfo() {
                log.debug("Checking datacenter info changes");
                AmazonInfo newInfo = AmazonInfo.Builder.newBuilder().autoBuild("eureka");
                if (!this.getDataCenterInfo().equals(newInfo)) {
                    log.info("Updating datacenterInfo");
                    ((AmazonInfo) this.getDataCenterInfo()).setMetadata(newInfo.getMetadata());
                }
            }

            private AmazonInfo getAmazonInfo() {
                return (AmazonInfo) getDataCenterInfo();
            }

            @Override
            public String getHostname() {
                AmazonInfo info = getAmazonInfo();
                final String publicHostname = info.get(AmazonInfo.MetaDataKey.publicHostname);
                return this.isPreferIpAddress() ?
                        info.get(AmazonInfo.MetaDataKey.localIpv4) :
                        publicHostname == null ?
                                info.get(AmazonInfo.MetaDataKey.localHostname) : publicHostname;
            }

            @Override
            public String getHostName(final boolean refresh) {
                return getHostname();
            }

            @Override
            public int getNonSecurePort() {
                return managementPort;
            }

            @Override
            public String getHomePageUrl() {
                return super.getHomePageUrl();
            }

            @Override
            public String getStatusPageUrl() {
                String scheme = getSecurePortEnabled() ? "https" : "http";
                return scheme + "://" + getHostname() + ":"
                        + managementPort + getStatusPageUrlPath();
            }

            @Override
            public String getHealthCheckUrl() {
                String scheme = getSecurePortEnabled() ? "https" : "http";
                return scheme + "://" + getHostname() + ":"
                        + managementPort + getHealthCheckUrlPath();
            }
        };

        AmazonInfo info = AmazonInfo.Builder.newBuilder().autoBuild("cloudconfig");
        instance.setDataCenterInfo(info);

        return instance;
    }


```
