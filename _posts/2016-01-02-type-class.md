---
layout: post
title: "Program with type class"
description: ""
category: 
tags: [functional-programming]
---

Suppose you start with a simple case class, with concrete domain-specific operations.

But you want to add more behaviors to your type, espeically those less related to domain operations, more on the functional relationships.

You need the implementation for the new behavior. A traditional way would be to include it in the type definition, often, this means
including mixin the trait which has the behavior you want.

One way to decouple it the mixined trait is to

1. Define a general type class that has the behavior you want
2. Define a instance of that type class, with that concrete type as type parameter. This instance will have implementation of that behavior
in that concrete type
3. this instance of the type class will be defined in the companion object of the type class
4. To use this implementation, pass in the type class as type parameter, and use implicitly to get the type class (NOTE: note type
instance)
5.Basically, treat type parameters as normal parameters pass into the function. According to Curry-Howard Isomorphism, each porposition int
he logic there is a coresponding type int he programing language and vice versa. And each proff of a given proposition there is a program of
the correspoidng type and vice versa

