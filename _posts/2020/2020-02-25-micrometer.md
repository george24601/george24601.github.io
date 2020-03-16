---
layout: post
title: "How micrometer keeps percentiles and publish to datadog"
description: ""
category: 
tags: [java, micrometer]
---

### Data structures (bottom up)
* StepDouble: threadsafe
  * By default, each step represent 1 min
  * value operations are done by `DoubleWriter`
  * Within the same step, the returned value of `poll()` will remain same. The value is reset at the each step
* Histogram: either 
  * hdr-based histrogram `TimeWindowPercentileHistogram` 
  * fixed boundary histograms `TimeWindowFixedBoundaryHistogram`
* Timer: has an instance of Histogram
* Meter: A named and dimensioned producer of one or more measurements. All the metrics construct we create in the application code, e.g., Counter, Timer, implements meter
* DatadogMeterRegistry: implements  `MeterRegistry` -> `StepMeterRegistry`. 
  * Maintains the list of meters
  * Associates a instance of `Timer` with a `HistogramGauges`, which translate histogram to the the percentile gauges (with the phi tag) we observe on DD

### DatadogMeterRegistry.publish()

* Gets the API URI: by default is the public API. Normally we sent to dogstatd URL on 127.0.0.1
* Partition the `Meter`s in the registry to a nested list of `Meter`s. By default, partition size is 10k 
* All data in these 10k meters will be published in a single POST call 
* By default, this method is triggered once per minute. See `PushMeterRegistry.start()`

