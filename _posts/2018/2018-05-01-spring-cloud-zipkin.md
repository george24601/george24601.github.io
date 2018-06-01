---
layout: post
title: "Zipkin in the Context of Spring Cloud" 
description: ""
category: 
tags: [architecture]
---

0. Such tracing tool is good for latency/performance troubleshooting, and investigating of frequently-occuring bugs. HOWEVER, you still need tradiitonal ELK-ish logging infrastructure to do customer support, mainly because sampling rate is kept low for performance 

1. Need to config message format in to follow sleuth's format. By default it listens to stdout appender

2. The out-of-the-box zipkin jar/docker doesn't handle service discovery and registration. You need to wrap zipkin-server in your own spring boot app to register it.

3. To disable zipkin via config, need to turn off both sleuth and zipkin 

4. Zipkin doesn't have access control, you probably need to build a reverse proxy for this.

5. Need to create zipkin db/tables before connection. It won't auto create

6. If you don't see some trace, make sure the very begining of the trace is recorded,i.e., if the very first receiver of the request is not recorded, the trace won't appear
