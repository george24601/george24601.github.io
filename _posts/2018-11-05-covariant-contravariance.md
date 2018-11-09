---
layout: post
title: "Covariance and Contravariance" 
description: ""
category: 
tags: [interview]
---
a <: b means a is a subtype of b

* Return types are covariants for function subtyping, that is,  if ta <: tb then t -> ta <: t -> tb 
* Argument types are contravariant for function subtyping, that is, if ta <: tb, then tb -> t <: ta -> t
  * Suppose you wrap your tb -> t type in a collection or a higher-order function, and you pass a ta -> t type, then during execution, the underlying type always expects passing in ta -> t. However, inside the function's code, because the signature is tb-> t, the actual type tb -> t may be passed to the param of ta type => contradiction! 
  * Similar reasoning on why in Java you can't pass List<String> to a method accepting List<Object>, because the method will add an actual Object to the underlying List<String>
* Class and types are DIFFERENT! Class defines an object's behavior, a type describes what fields an object has and what messages it can respond to. Subtyping is a question of substitutability and what we want to flag as a type error
* Subtyping is a bad substitute for Generics, and conversely, generics are a bad substitute for subtyping.
