---
layout: post
title: "Guide lines on monitors and alerting - summary from Datadog docs"  
description: ""
category: 
tags: [k8s]
---

# Source
[Monitoring 101](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)

[Alerting 101](https://www.datadoghq.com/blog/alerting-101-status-checks/)

#Monitoring

1. Monitor your work metrics, including rps, 90th percentile latency, success rate in the last window, rate of error (per window or relative to throughput), 

2. Monitor your resource metrics, including resource utilization, resource saturation, errors internal to resources, and resource availability

3. Monitor change events, including releases, and scaling up and down.

#Alerting

1. Feel free to add alerts, but only page when the symptom impacts user or the user-facing impact is very likely to ensue.

2. For alerts requires intervetion but not right away, email and slack is enough.

