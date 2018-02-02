---
layout: post
title: "Running Consul in ECS" 
description: ""
category: 
tags: [middleware]
---

Setting up consul
-----------

1. For the deployment, we use an autoscaling group (ASG) of fixed size, i.e., min size = max size = 3 or 5, and set -bootstrap-expectto the cluster size. Desired size for ECS and ASG are equal to the min size 

2. Use ```-retry-join "provider=aws tag_key=$TAG_KEY-ecs tag_value=$TAG_VALUE"``` to mark the consul EC2 instances. 
Other flags don't seem to work in my experiments.

However, this  retry-join by tag actually gave me a lot of trouble in DR drills, namely the handle the case where majority of instances are replaced by autoscaling group. In the end I took it out and manually join the cluster. Consul is smart enough to rejoin previously know cluster 

3. Need ```-client 0.0.0.0``` so that the consul inside docker can receive traffic from outside the docker,i.e., consul will listen to all network interfaces

4. The image we use is based on the offical consul image, but we have to modify docker-entrypoint.sh to include EC2 API call. So that we can fix the node id to the EC2 instance ID, and address to the EC2 private IP. I also tried invoking the curl call from terraform or supply the command from docker command, but my efforts are not successful

A working example, inside the docker-entrypoint.sh

```
set -- "$@" agent -server -ui \
        -data-dir="$CONSUL_DATA_DIR" \
        -config-dir="$CONSUL_CONFIG_DIR" \
        $CONSUL_BIND \
	-bootstrap-expect=$BOOTSTRAP_EXPECT \
	-node $(curl -s http://169.254.169.254/latest/meta-data/instance-id) \
	-retry-join "provider=aws tag_key=$TAG_KEY tag_value=$TAG_VALUE" -client 0.0.0.0 \
	-advertise $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)
```


Setting up consul client
---------

1. EACH instance in your service cluster needs consul agent and registrator running. This can be done by using ECS api call in user data AND adding that to systemd, or just force ECS task capacity same as cluster size.

2. By default, i.e., without -dev or -server, agent runs in client mode. It can reuse our custom consul server docker image above.

3. Registrator and consul client should not be in the same task, because during start, registrator should wait for the consul client to get ready. Otherwise, the registrator ECS task would fail.

4. The registrator automatically discovers the service name by its BASE docker image name. This means all our deployments should use a single ECR with different tags

5. However, most likely you don't need the registrator, because most consul client package will include the registration

6. Consul client agent container should run in the host mode, so does all its client programs, i.e., we should manage ports to avoid conflicts here. Note if the network is host mode we don't need to set ```-client```

7. Unlike server agent, client agent can and should rejoin by tag, since its stateless 

Gotchas
---------------

1. For spring cloud consul, MAKE SURE you set spring.cloud.consul.discovery.queryPassing = true. Otherwise, spring's consul library will grab unhealthy instance as well. This caught us off guard during our DR drills.

2. To deregister a service instance, use the agent API instead of catalog API on the same host the service instance is running. The service ID is the "ServiceID" field returned by the GET request

3. ECS has no auto restart API. So as part of deployment, we need to register a new service definition and then update the service, as shown by many other people. However, pay attention to the minium health threasold you set. If it is too high (> 0 if you have only 1 instance), the auto restart upon service update will not kick in, because otherwise, the # of health services will drop below your min thresold
