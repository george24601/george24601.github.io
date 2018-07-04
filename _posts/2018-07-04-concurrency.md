---
layout: post
title: "Common concurrency problems" 
description: ""
category: 
tags: [arch]
---

# Producer - consumer problem

Can have multiple producers - consumers

```python

mutex = 0
cur_cnt = 0 
remain_cap = N //q with max size N
q = []

def producer(item):
	down(remain_cap) //Note the order of downs can't be swapped
	down(mutex)
	q.append(item)	
	up(mutex)
	up(cur_cnt)


def consumer():
	down(cur_cnt)
	down(mutex)
        item = q.pop()
	up(mutex)
	up(remain_cap)
	return item

```

# Readers - writers problem

```
reader_count = 0
read_lock = 1
write_lock = 1

def read():
	down(read_lock)
	reader_count++
	if reader_count :
		down(write_lock)
	up(read_lock)
	read_op()
	down(read_lock)
	reader_count--
	if reader_count == 0 :
		up(write_lock)
	up(read_lock)
	
def write(v):
	down(write_lock)
	write_op(v)
	up(write_lock)
```

# Dining philosophers problem


# Cigarette smokers problem

# Sleeping barber problem
