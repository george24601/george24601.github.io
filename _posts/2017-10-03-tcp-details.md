---
layout: post
title: "On TCP"
description: ""
category: 
tags: [network, architecture]
---

1. Even though we call tcp "packets", the correct term is segment for TCP, datagram for IP, and frame for link layer

2. The length of the data section is not in the TCP segmet header, and thus needs to be inferred

3. During the 3-way handshake of establishing connection, the initial sequence number on either side is randomly chosen

4. During the 4-way handshake of connection terminiation, each side terminates independently. Also, the side that sends the first FIN will wait for a timeout period after sending out the final ACK,  before closing the connection, so that the local port is unavailable for new connections. Some optimization turns the handshake into 3 ways, by letting the receiving end send FIN & ACK together, since they are neighboring steps.

5. Most implementaions maps a session to an OS process, with address and port (0 to 65535) as the identifier. Note that port is only on the TCP layer, IP is only on the IP layer.

6. The window field specifies # of bytes the other side is willing to receive
