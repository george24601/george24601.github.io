---
layout: post
title: "Samza Job Patterns"
description: ""
category: 
tags: [Samza]
---
Overall, remember that each samza container runs only in one thread. Therefore, performance is very sensitive to your logic

1. Do not use MessageCollector outside process()

2. Try to limit local storage I/O. Our performance test shows RockDB, although fast, is CPU heavy

3. Try not to include cross process/network communication. If you have to, try to batch it to amortize cost. Do not println.

4. Take at-least-once delivery of messages into account

5. At-least-once semantics means you should apply the operation, update state, and then persist it

6. A more functional-style design will allow easier unit testing, although their next release will include better unit testing support

7. Most reliable tools to solves performance problem: Samza metrics, tcpdump, jstack.
