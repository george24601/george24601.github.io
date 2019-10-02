---
layout: post
title: "On Spring AOP"
description: ""
category: 
tags: [interview,java]
---

### cglib vs dynamic proxy

* JDK dynamic proxy works only for the interfaced classes, and as the name suggests, it is implemented by the proxy pattern/composition. This means to trigger the annotation, the dynamic proxy has to be called from outside this class. Implemented by reflection
* cglib works doesn't have this restriciton, because it implemented via inheritance. This also means annotaion works only for the public methods, because only they can be inherited. 
