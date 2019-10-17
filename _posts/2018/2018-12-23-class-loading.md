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
  * execute type constructor clinit() method
  * () is to construct types: which has static code block or static assign value
  * () method is to execute instances, which runs () first
  * Init of class variables will be in the order of declaration

### Types of class loader

* Bootstrap ClassLoader: load JAVA_HOME/lib and boot classpath `JAVA_HOME/lib/rt/jar`
* Extension ClassLoader: load JAVA_HOME/libext and java.ext.dirs. Parent is Bootstrap CL
* Application/System ClassLoader: classpath in the normal sense. Parent is Extension CL. Loads from ENV VAR and class.path
* User ClassLoader: inherits form Application ClassLoader. Note classes loaded by this CL can be unloaded, but not the other 3


### Parent delegation model for loadClass()

* See if the requested type has already been loaded into this class loader's namespace (via findLoadedClass()). If so, return the Class instance for that already-loaded type.
* Otherwise, delegate to this class loader's parent loader. If the parent returns a Class instance, return that same Class instance.
* Otherwise, invoke findClass(), which should attempt to locate or produce an array of bytes that define the desired type. If successful, findClass() should pass those bytes to defineClass(), which will attempt to import the type and return a Class instance. If findClass() returns a Class instance, loadClass() returns that same Class instance.
* Otherwise, findClass() completes abruptly with some exception, and loadClass() completes abruptly with the same exception.
* When the loadClass() method of a class loader successfully loads a type, it returns a java.lang.Class object to represent the newly loaded type.
* Note that tomcat does not really follow this model


### Custum class loader

* Types loaded through the bootstrap class loader will always be reachable and never be unloaded. Only types loaded through user-defined class loaders can become unreachable and be unloaded by the virtual machine. A type is unreachable if its Class instance is found to be unreachable through the normal process of garbage collecting the heap.  
* Use case:
  * Byte encrption: use tool to encrypt class file. when running, use customized ClassLoader to decrypt file content and then load the decrypted bytecode 
  * hot load class file
  * load class at specific location, e.g., URL
* to uniquely identify a type loaded into JVM requires the fully qualified name and the defining class loader. Every Class object has reference to its class loader

### When will java init the class?
* create class instance type
* use class/interfaces' static val/method. Note that before that the interface of the class will not be inited. Note that this class's instanct variable types will not be inited
* Class.forName(). Note that Classloader.loadClass() will not init the class immediately
* init a class's subclass
* Tagged during JVM's start

