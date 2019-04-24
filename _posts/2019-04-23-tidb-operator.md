---
layout: post
title: "Notes on TiDB Operator"
description: ""
category: 
tags: [tidb]
---

TO = TiDB Operator
TPR = (k8s) third party resource
PV = persistent volume

* Use k8s's TPR API to encapsulate ops domain knowledge into the operator, and then inject the module to the k8s. TO to K8s is like bash to OS
* We should use local volume to persist for prod, but we can use networked volume for backup. Know that local PV uses special rules for failover
  * When one machine is down, the pod's local PV may not be restored => stateful set itself is not enough
  * In k8s cases, to do IOPS isolation, have to use one PV one physical disk. Bind mount does not support volume and IOPS separation
  * Due to security concernts, load network PV to other nodes is dangerous,e.g., double write due to eager write
* Set resource limit same as request to guarantee QoS

### Debug pending pod

When a pod is pending, it means the required resources are not satisfied. The most common cases are:

* CPU, memory or storage insufficient
* Storage class not exist or no PV available
* By default TiDB service is exposed using NodePort. You can modify it to ClusterIP which will disable access from outside of the cluster. Or modify it to LoadBalancer if the underlining Kubernetes supports this kind of service.
* It's recommended to do horizontal scaling other than vertical scaling when workload increases. Just modify the replicas of PD, TiKV and TiDB in values.yaml file.
* By default the monitor data is not persistent, when the monitor pod is killed for some reason, the data will be lost. This can be avoided by specifying monitor.persistent to true in values.yaml file.

### How TO works

1. Create a k8s config map for tidb storage. Storage's physical resource is inside the config, e.g., machine's IP, disk's corresponding folder
2. Create a tidb-volume TPR, this resource reads the physical disk status and converts the disk resources into a TPR too
3. Create a volume controller to mark the usage status of the resources, VC also montiors config map's physical resource in RT
4. Pakcage the local disk resource boot instances
5. Create daemonset to maintain disk's resource usage, e.g., when a tikv node is down, you need to delete and clear data.
