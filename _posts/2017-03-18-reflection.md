---
layout: post
title: "Reflections on Reflection"
description: ""
category: 
tags: [codeforces]
---
Reflection is commonly used with annotation
```
1. JUnit look for the @Test annotaion, and then use method.invoke(ourObject, null)

2. Similarly, we can introduce our custom anotation, and then find it via method.isAnnotationPresnet(OurAnnoation.class), so that we then can method.invoke

```

Reflection is commonly used for class instantiation
```
1. Often we want to specify want class to run in config, we can just specify the full calss path in the AppSettings, and then instantiate it via Class.forName and then class.newIntance. Note that with Guice, we can just use Injector.getInstance(class)

2. Similarly, we can pass the class name as part of serialization form, and on the consumer side, along with the states, and on the consumer side, use Constructor.newInstance or class.newInstance

3.One other use is when we don't want to supply all parameters to constructer, e.g., via dependency injection. We can just supply only the types we need, and then custruct the array with the unsupplied with null

```

