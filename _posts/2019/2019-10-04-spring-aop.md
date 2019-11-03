---
layout: post
title: "On Spring AOP"
description: ""
category: 
tags: [interview,java]
---

### Terms

* Join point: A point during the execution of a program, such as the execution of a method or the handling of an exception.
* Advice: Action taken by an aspect at a particular join point,i.e., the actual code, think it as the method of an apsect. Spring AOP currently supports only method execution join points (advising the execution of methods on Spring beans).
* Introduction: (Also known as an inter-type declaration). Declaring additional methods or fields on behalf of a type. Spring AOP allows us to introduce new interfaces (and a corresponding implementation) to any proxied object.
* Aspect: advice + introduction

### cglib vs dynamic proxy

* JDK dynamic proxy works only for the interfaced classes, and as the name suggests, it is implemented by the proxy pattern/composition. This means to trigger the annotation, the dynamic proxy has to be called from outside this class. Implemented by reflection
* cglib works doesn't have this restriciton, because it implemented via inheritance. This also means annotaion works only for the public methods, because only they can be inherited. 
