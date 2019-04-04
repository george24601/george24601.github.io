---
layout: post
title: "Desgin of property-based testing lib from functional programming in Scala"
description: "Functional Programming"
category: 
tags: [functional-programming]
---

Basic idea: 

  1. Framework generates values and run propery checks against those values to ensure property is satisfied
  
  2. Programmer comes up with algebraic properties of the API to be tested

  3. Framework should be able to minimize failed test and generate exhaustive values for the properties space


Design process:
  
  1. Obviously, we need a Gen[A] to represent generatoer
  
  2. To create a property, we use forAll(Gen[A]) (A => Boolean) : Prop. A => Boolean represent the algebraic rule specified by the
programmer

  3. Prop should be composable, i.e., it needs to support && and || both of type Prop => Prop, i.e., accepts another clause and returns a
combined clause

  4. Prop needs to be able to check, i.e., need a check: Either[SuccessInfo, FailureInfo]

  5. For Gen, often we need context-sensitive generation, e.g., first generate the size of the list, and then generate list based on size,
so Gen is a monad and needs to support flatMap

  6. For Gen to know how to generate, we need to pass in a State[RNG, A], where State itself is a RNG => (A, RNG)

  7. For Prop to how how to run test, we need to pass the logic over, i.e., how many cases to pass before we show the result? TestCases =>
Result

  8. Since Prop is responsible for running the check, it needs to know how to generate as well. Instead of TestCases => Result, we need a
(TestCases, RNG) => Result, so Gen can accept that rng and run
