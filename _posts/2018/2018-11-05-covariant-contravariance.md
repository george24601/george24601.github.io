---
layout: post
title: "Generic, covariance, and contravariance" 
description: ""
category: 
tags: [interview, java]
---

* a <: b means a is a subtype of b
* The overall goal of covariance and contravariance is to reduce the case where type check passed but runtime gives typing error. 
* The main use case is when the type is in a higher-order function or generic collections. 
  * Adding a type to a generic collection is somewhat similar to the function call in typing rules, i.e., the generic type is the parameter type 
  * Reading from a generic colleciton is somewhat similar to the function return in typing rules, i.e., The generic type beais the returned type

### Covariance

* Return types are covariants for function subtyping, that is,  if tsub <: tsuper then t -> tsub <: t -> tsuper 
* Use <T extends super> to mark the upper bound of generic (at super). 
  * Required when we return a generic collection, so that we have a guarantee that we can get super at least from collection
  * No guarantee on what we can write, because we don't know the concretely underlying type, which will be casted to on read.
* Class and types are different! Class defines an object's behavior, a type describes what fields an object has and what messages it can respond to. Subtyping is a question of substitutsubbility and what we want to flag as a type error

### Contravariance

* Argument types are contravariant for function subtyping, that is, if tsub <: tsuper, then tsuper -> t <: tsub -> t
  * Suppose you wrap your tsuper -> t type in a collection or a higher-order function. If you pass a tsub -> t type, then during execution, the underlying type always expects passing in tsub. However, because the signature is tsuper-> t, we can still pass the tsuper to the concrete tsub-> t function without violating type check => contradiction!
* Similar reasoning on why in Java you can't pass List<String> to a method accepting List<Object>, because the method will add an actual Object to the underlying List<String>
  * Note that in the source they are all stored in an object array, and cast to the type on read
  * We can't pass List<Object> to List<String> functions either, due to the covariance rule
* Use <T super subtype> to mark the lower bound of generic (at subtype). This means we are 100% safe to add only the subtype and its subtypes to the generic collections    

