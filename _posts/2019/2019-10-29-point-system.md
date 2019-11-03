---
layout: post
title: "System design: point system for ecommerce"
description: ""
category: 
tags: [interview]
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

On top that, standard idempotency, MQ, retry

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
