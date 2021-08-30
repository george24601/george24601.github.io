---
layout: post
title: "Guidelines on monitors and alerting"  
description: ""
category: 
tags: [ops, datadog]
---

* Feel free to add alerts, but only page when the symptom impacts user or the user-facing impact is very likely to ensue.
* For alerts requires intervetion but not right away, email and slack is enough.
* We highly recommend you select "Do Not Require" for sparse metrics, otherwise some evaluations will be skipped - but this may trigger more false alarms if the window is too short
* One idea is to create a composite monitor, one for the errorneous data, and one for the amount of data, and the alert fires only when both are satisfied
* Datadog generally recommends against using interpolation for count/rate metrics in monitors, i.e., interpolation works only with gauge metrics
* Short evaluation window means even shorter time buckets. In monitors with division over short evaluation windows. If metrics for the numerator and denominator aren’t both available at query time, you could get unwanted evaluation values.


### References
* [What is the "Do (not) require a full window of data for evaluation" monitor parameter?](https://docs.datadoghq.com/monitors/faq/what-is-the-do-not-require-a-full-window-of-data-for-evaluation-monitor-parameter/)
* [Monitor Arithmetic and Sparse Metrics](https://docs.datadoghq.com/monitors/guide/monitor-arithmetic-and-sparse-metrics/)
* 
