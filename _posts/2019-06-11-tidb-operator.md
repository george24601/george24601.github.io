---
layout: post
title: "How tidb operator constructs aws eks cluster"
description: ""
category: 
tags: [k8s, tidb]
---

Context: we want to provision multiple tidb clusters inside the same EKS cluster

### Terraform
* Uses different worker groups to host different components, each worker group with specific taint and node label at the same time, e.g.,
    ```
--register-with-taints=dedicated=pd:NoSchedule --node-labels=dedicated=pd
    ```
* Each worker group maps to a ASG, and we can add tags to the user group itself then. Note that by default the monitor group doesn't have extra tags
* The user data at each worker group just configs docker group and format and mount nvme disk
* Uses a null resource provisioner to apply the following k8s manifests, CRD, local volume provisioner, gp2 storage class, tiller rbac, in order
  * inits helm and waits until it completes
  * releases a tidb-operator via helm chart of the same name to tidb-admin namespace
  * releases a tidb-cluster via helm chart of the same name, to tidb namespace. Note that this chart accepts data.template_file.tidb_cluster_values as params
  * Then the null resource provisioner will keep polling via kubectl until pd, tidb, and tikvs are ready
* Note that elb and network access rules are not provisioned explicitly here 
* The helm release for tidb-cluster are done by tf module, where `tidb-cluster-values.yaml.tpl` provides params to

    ```
    resource "helm_release" "tidb-cluster" {
  depends_on = ["helm_release.tidb-operator"]
  name = "tidb-cluster-${var.cluster_name}"
  namespace = "tidb"
  chart = "${path.module}/charts/tidb-cluster"
  values = [
    "${data.template_file.tidb_cluster_values.rendered}"
  ]
}
```


### Tidb cluster helm chart
* Check `pkg/apis/pingcap.com/v1alpha1/types.go` for the template values, important ones include: 
    ```go

    // TidbCluster is the control script's spec
type TidbCluster struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata"`

	// Spec defines the behavior of a tidb cluster
	Spec TidbClusterSpec `json:"spec"`

	// Most recently observed status of the tidb cluster
	Status TidbClusterStatus `json:"status"`
	}

    type TidbClusterSpec struct {
	SchedulerName   string              `json:"schedulerName,omitempty"`
	PD              PDSpec              `json:"pd,omitempty"`
	TiDB            TiDBSpec            `json:"tidb,omitempty"`
	TiKV            TiKVSpec            `json:"tikv,omitempty"`
	TiKVPromGateway TiKVPromGatewaySpec `json:"tikvPromGateway,omitempty"`
	// Services list non-headless services type used in TidbCluster
	Services        []Service                            `json:"services,omitempty"`
	PVReclaimPolicy corev1.PersistentVolumeReclaimPolicy `json:"pvReclaimPolicy,omitempty"`
	Timezone        string                               `json:"timezone,omitempty"`
}
    ```



