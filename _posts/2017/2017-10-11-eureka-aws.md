---
layout: post
title: "AWS specific code in Spring Cloud Netflix - Eureka"
description: ""
category: 
tags: [middleware, eureka]
---

Note that Spring Cloud Eureka adds a layer of abstraction on top of vanilla netflix eureka, even though they have similar names such as client/server/registry e.t.c. Therefore, when navigating through the code, I often run into definition that embedded inthe vanilla jar

Eureka Client - Client config
----------
1. Default region is us-east-1

2. serviceUrl is az -> fully qualified URL map, default mapping is "defaultZone" -> "http://localhost:8761/eureka" 

3. availabilityZones is a region -> az map. Default is empty 

Eureka Client - Instance config
---------

1. aSGName - autoscalling group associated with this instance

2. dataCenterInfo - data center where is this instance is deployed - DataCenterInfo.Name.MyOwn

Eureka Server - Bootstrap
---------
1. awsBinder - during serverContext init, we check if applicationInfoManager.getInfo() is from aws

```java
	protected boolean isAws(InstanceInfo selfInstanceInfo) {
		boolean result = DataCenterInfo.Name.Amazon == selfInstanceInfo
				.getDataCenterInfo().getName();
		log.info("isAws returned " + result);
		return result;
	}
```

Eureka Server - Server Config
---------
1. aWSAccessId

2. aWSSecretKey

3. aWSBindingStrategy, default EIP

Eureka Server - Controller
-------
1. In header
```java
	if (info.getName() == DataCenterInfo.Name.Amazon) {
			AmazonInfo amazonInfo = (AmazonInfo) info;
			model.put("amazonInfo", amazonInfo);
			model.put("amiId", amazonInfo.get(AmazonInfo.MetaDataKey.amiId));
			model.put("availabilityZone",
					amazonInfo.get(AmazonInfo.MetaDataKey.availabilityZone));
			model.put("instanceId", amazonInfo.get(AmazonInfo.MetaDataKey.instanceId));
		}
```

2. When populating apps on controller, it does similar check as above

Outstanding Questions
------------
1. How is an instance of ApplicationInfoManager is populated?
2. In Eureka client, how is the region info populated?
3. Why my spring cloud eureka is not logging while the original is?
