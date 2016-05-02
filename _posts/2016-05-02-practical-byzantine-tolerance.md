---
layout: post
title: "Practical Byzantine Tolerance"
description: "My notes and study questions"
category: 
tags: [classic]
---

Most papers assume non-byzantine failure. This one shows that how (hard) to defend against it

```
  1. Suppose we want to tolerate f failures in the total of n nodes,  Why n > 3f?

  2. Why not feasible to offer fault-tolerant privacy in the general case?

  3. Why we have to use synchrony to provide liveness?

  4. Since failure is byzantine, how do we protect against spoofing, replaying, and corruption?

  5. From the client perspective, what does the protocol look like?

  6. What are the steps of the multicast? What are their roles?

  7. What the condition for a back to accept a pre-prepare message?

  8. When will prepare(m,v,n, i) become true? Why this guarantees that non-faulty replicas agree on a total order?

  9. How are watermarks progressed?

  10. What is a stable checkpoint? How to prove a checkpoint is stable?
```
