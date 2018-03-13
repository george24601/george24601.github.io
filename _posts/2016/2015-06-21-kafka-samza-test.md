---
layout: post
title: "Testing Samza and Kafka applications" 
description: ""
category: 
tags: [kafka, samza]
---
###Integration testing by code###
	Kafka supporting local testing utils, and yarn supports ThreadLocalFactory specfically testing,i.e., in your integration testing code, you can spawn a local kafka/zk cluster, and submit the samza job via yarn API.	

	The whole samza job config file is passed into yarn API as string k-v pairs anyway, so we are able to simulate the settings easily by code.

	However, you still need to write code to populate the topic and read the output topic. The localhost zookeeper you spawn may be in conflict with the running one in your local dev environment, or you have multiple integration tests and they run in parallel instead of sequentially

	You also need to remember to turn on/shut down your testing utils as part of beforeAll/afterAll operations. 

###Simulate local state###
	Option 1: sacrifice the encapsulation and expose the init logic, so that you can pass in your fake local storage, normally in the form of CachedKVStore.
	
	Option 2: Populate the messages that defines the state as well when you are populating the topic in the test environment

	Option 3: wrap local state creation in a protected test method, add a subtype in testing frame so you can provide mock implementation

###Use of mock objects###
	Mock object is valuable at discoverying the interaction between YOUR OWN componenets,i.e., directly contradicts the definition of integration testing, which requires us to cross the boundary

	An alternative approach is to wrap external dependency with your own interface, so you can mock your own wrapper. In practice, such lightweight wrapper doesnt give enough benefit at the cost of additional types, and the wrapper itself becomes test liability,i.e., wrapper is useful only when it contains non-trivial concrete logic.
