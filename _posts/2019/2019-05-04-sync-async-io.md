---
layout: post
title: "On IO"
description: ""
category: 
tags: [java, interview]
---

* Sync/async vs blocking/non-blocking
  * first one is about the whether the caller get the result immedately, and second one is aobut the where is caller waits
  * In the context of IO, Sync often means blocking
* One example Sync + non-blocking will be self-spinning, which is what selector in java NIO does. The selector itself is sync + blocking, but underlying it will be busy polling the sockets, i.e., epoll() sys call.
* In fact, most non-blocking is implmented by self-spinning, but encapsulated but encapsulated as sync/blocking. 
* Java AIO is purely async + non-blockng, it returns immediately and you need to pass the callback function at construction
* Cocurrency is the fact that multiple tasks can progress over a period of time. Parallelism means they can progress AT THE SAME TIME
* Note that data needs to be copied from kernel space buffer to user space buffer, and vice versa. This motivates the zero-copy techinique
  * map the kernel space address and user process's address into the same physical memory
  * mmap maps the file/object into user process's address space. Note that we still need to copy from kernel read buffer to kernel socket buffer if we want to send to the network drive.
  * Sendfile means data transfer happens only inside the kernel space, and kernel buffer's memeory address and offset will be recorded into socket buffer. This saves the cpu copy from kernel read buffer to kernel socket buffer
  * In java, MapppedByteBuffer, DirectByteBuffer, Channel-to-Channel

### Unix IO models
* Blocking IO: process uses `recvfrom`, which copies from kernel space to user space
* Non blocking: polling to check if kernel has prepared the data yet, still via `recvfrom`
* Event-driven: process ues `sigaction`, kernel will notific with SIGIO, and then process `recvfrom`
* Io-reuse: multiple process's IO can register on the smae channel, i.e., `select()`. Note that after `select()`, the process has to wait until at least one required data is ready and then return
* Async io:  `aio_read` with FD, buffer pointer, buffer size, after the kernel prepared the data, will copy it to the user space, and send signal



