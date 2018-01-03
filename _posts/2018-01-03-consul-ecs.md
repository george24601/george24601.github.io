---
layout: post
title: "Running Consul in ECS" 
description: ""
category: 
tags: [middleware]
---

Setting up consul server
-----------

1. For the deployment, we use an autoscaling group of fixed size, i.e., min size = max size = 3 or 5, and set -bootstrap-expectto the cluster size 

2. Use ```-retry-join "provider=aws tag_key=consul-ecs tag_value=$TAG_VALUE"``` to mark the consul EC2 instances. Other flags don't seem to work in my experiments 

3. Need ```-client 0.0.0.0``` so that the consul inside docker can receive traffic from outside the docker

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

Setting up consul agent
---------

1. EACH instance in your service cluster needs consul agent and registrator running. This can be done by using ECS api call in user data AND adding that to systemd, or just force ECS task capacity same as cluster size.

2. By default, i.e., without -dev or -server, agent runs in client mode. It can reuse our custom consul server docker image above.

3. Registrator and consul client should not be in the same task, because during start, registrator should wait for the consul client to get ready. Otherwise, the registrator ECS task would fail.

4. The registrator automatically discovers the service name by its BASE docker image name. This means all our deployments should use a single ECR with different tags

3. Registrator should 
