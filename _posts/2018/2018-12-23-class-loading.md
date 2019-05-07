---
layout: post
title: "On JVM Class Loading"
description: ""
category: 
tags: [jvm, interview]
---

### Class load sequence of actions

* load - done by the class loader
  * get byte stream via FQTN
  * transform byte stream to metaspace runtime data structure
  * create java.lang.Class for this type
* Linking - Verification, including file format, meta data, byte code, symbol reference
* Linking - Prep
  * allocate memories for class variable and assign init values inside metaspace
  * However for final value, at this same it will be that value already
* Linking - Analyze/Resolution. Note this step can be delayed after init
  * Compiler generate symbolic ref including class name, class method name, params type, and return types
  * the class does not even know its own method and string's address yet. Need to replace symbolic refrence with direct reference
* Init
  * execute type constructor () method (clinit?)
  * () is to construct types: which has static code block or static assign value
  * () method is to execute instances, which runs () first

### Types of class loader

* Bootstrap ClassLoader: load JAVA_HOME/lib and bootclasspath
* Extension ClassLoader: load JAVA_HOME/libext and java.ext.dirs
* Application ClassLoader: classpath in the normal sense.
* User ClassLoader: inherits form Application ClassLoader
* When do you need your own class loader??


### Parent delegation model for loadClass()

* See if the requested type has already been loaded into this class loader's namespace (via findLoadedClass()). If so, return the Class instance for that already-loaded type.
* Otherwise, delegate to this class loader's parent loader. If the parent returns a Class instance, return that same Class instance.
* Otherwise, invoke findClass(), which should attempt to locate or produce an array of bytes that define the desired type. If successful, findClass() should pass those bytes to defineClass(), which will attempt to import the type and return a Class instance. If findClass() returns a Class instance, loadClass() returns that same Class instance.
* Otherwise, findClass() completes abruptly with some exception, and loadClass() completes abruptly with the same exception.
* When the loadClass() method of a class loader successfully loads a type, it returns a java.lang.Class object to represent the newly loaded type.


### Custum class loader
* Types loaded through the bootstrap class loader will always be reachable and never be unloaded. Only types loaded through user-defined class loaders can become unreachable and be unloaded by the virtual machine. A type is unreachable if its Class instance is found to be unreachable through the normal process of garbage collecting the heap.  
* Use case:
  * Byte encrption: use tool to encrypt class file. when running, use customized ClassLoader to decrypt file content and then load the decrypted bytecode 
  * hot load class file
  * load class at specific location, e.g., URL
* to uniquely identify a type loaded into JVM requires the fully qualified name and the defining class loader. Every Class object has reference to its class loader

