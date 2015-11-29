---
layout: post
title: "Code Jam 2008 1C: Ugly number"
description: "chinese remainder theorem"
category: 
tags: [google-code-jam]
---

By Chinese remainder theorem, because 2,3,5,7 are coprime, we know any answer can be reduced to a number in mod 2*3*5*7 group,i.e., one
dimenstion of the state is the element in the mod 210 group.

Another dimension is the position in the string.

At each step in the string, we have 3 choices, concatenate, +, or -, note that we do not know the position into which we insert signs  => we
need to try them all!

Gotchas
```
1. Note that we can not 2^64 is only 19 digits, so we can not convert the whole string directly into numbers. Fortunately num * 10 + digit
preverses its mod under Group 210, because 210 is divided by 10. You can prove this by induction, adding digit by digit


2. Alternatively, the state can be [start][end][mod] instead of [end][mod] to avoid trying all in loops


```


Starting state is ways[firstDigit] [0] = 1, ways[other memember in mod 210 groupd] [0] = 0;

Moreover, if number % 210 is divisible by 2,3,5, or 7, we know itself must be divisible by 2,3,5,or 7.

if we want to find all ugly numbers, since all can be reduced to one of 210 choices, we can brute force search and filter out non-ungly ones
