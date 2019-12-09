---
layout: post
title: "System design: point system for ecommerce"
description: ""
category: 
tags: [interview, design]
---

All e-commerce order systems follow very similar patterns

### Requirements
* Points gained via activities on the site
* use points to redeem gifts

### Data structure

* Account table to keep track of points of the user
* Need a txn table to track redemption history (user_id, product_id, used_points)
* Storage and shipping module
  * type: is it purchase or point? 
  * txn id: the txn record of the redeem hisotry 
  * Product id, cnt,....etc
  * tracking number, for 3rd party courier

### Idempotency
* Get token from service with TTL
  * On submit, try deletion. If does not work, reject the request
* Optimisitc lock: version col on DB row
* Some people propose distributed lock too. But I don't see point in it 
* API needs to require both source and seq as params

### General e-commerce components
* Catalog service for products
* Shopping carts
* Order system
  * Order itself
  * Async processing: coupon, push notification
  * Query
  * Return/Refund
  * Integration with 3rd party courier
* Payment system
  * Often calls back to the order system to ACK
* Delivery/Storage system

### Order phases
* Before generating order: txn systems. Online/sync
  * shopping carts - can stay in cache
  * order details
  * fee calculation
* Generate order: order system. Online/sync
  * balance
  * point and coupon
  * stock freeze
  * generate order and item
* After generation: fulfillment. Offline/async
  * reivew
  * delivery
