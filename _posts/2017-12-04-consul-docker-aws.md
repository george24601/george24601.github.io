---
layout: post
title: "Running Consul in Docker on AWS" 
description: ""
category: 
tags: [middleware]
---

Problems to watch out for
---------
1. Run it on ECS or not?

2. During the restart, what if the consul process is not longer co-located with data volume, or even worse, loaded a stale data volume

3. What network mode to use? What IP to broadcast?

4. Raft protocol size is fixed to 3 or 5 in practice, this means our cluster running Consul should be 3 or 5.

[Original Post](https://aws.amazon.com/blogs/compute/service-discovery-via-consul-with-amazon-ecs/)

[CloudFormation Template](https://github.com/awslabs/service-discovery-ecs-consul/blob/master/service-discovery-blog-template)

Consul server starts

```bash
docker run -d --restart=always -p 8300:8300 -p 8301:8301 -p 8301:8301/udp"
                                            "-p 8302:8302 -p 8302:8302/udp -p 8400:8400 -p 8500:8500 -p 53:53/udp",
                                            "-v /opt/consul:/data"
                                            "-h $(curl -s http://169.254.169.254/latest/meta-data/instance-id)",
                                            "--name consul-server progrium/consul -server -bootstrap",
                                            "-dc" INJECT_HERE
                                            "-advertise $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)",
                                            "-ui-dir /ui"

```

Consul agent starts
```bash
"docker run -d --restart=always -p 8301:8301 -p 8301:8301/udp",
                                            "-p 8400:8400 -p 8500:8500 -p 53:53/udp",
                                            "-v /opt/consul:/data -v /var/run/docker.sock:/var/run/docker.sock",
                                            "-v /etc/consul:/etc/consul",
                                            "-h $(curl -s http://169.254.169.254/latest/meta-data/instance-id)",
                                            "--name consul-agent progrium/consul -join" INJECT_HERE
                                            "-advertise $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4) -dc" INJECT_HERE
                                            "-config-file /etc/consul/consul.json"
```

Note
-------

1. -v, --volume=[host-src:]container-dest[:<options>]: Bind mount a volume.

2. -h, set the host name

3. -advertise, 

4. /etc/consul/consul.json. leave_on_terminate: true

5. Although /etc/ecs/ecs.config is defined, Consul does not run as a ECS task or service.

6. 169.254.169.254 is a reserved endpoint to query current instance's metadata

7. It also has a glidelab/registrator component

8. --net

9. progrium/consul is not the official consul docker image, which is hashicorp/docker-consul
