---
layout: post
title: "Internal Services on HTTPS" 
description: ""
category: 
tags: [infrastructure]
---

HTTPS requires an SSL/TLS certificate, which in turn requires a domain name for your internal service.

Two options

1. use a .local domain, e.g., our vault is on vault.service.local. Note that this domain is a reserved TLD by the internet standard

2. use a private subdomain in a public domain, e.g., vault.internal.mycompany.com.

Based on my research, there is no conclusion on which way is preferred. The major benefit of the second appraoch is that you can sign the certifcate with the public ca, whereas in the first case, you will need your own CA to sign it

In either case, this means you need to send up DNS multi-cast, e.g., via dnsmasq and internal DNS resolver, so that domains with these specifal prefixes will be resolved by your company's internal DNS.

If you use the first apprach, make sure add ca.cert to SSL, NSS (which curl uses), and java keytool. 

Another catch is when you want to place a (most likely internal) ELB in front of your internal service. The ELB needs to be a TCP ELB, so that it won't try terminating the SSL connection
