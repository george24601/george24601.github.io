---
layout: post
title: "Prometheus file structure"
description: ""
category: 
tags: [prometheus, arch]
---

* Every two hours' data is in the same block. Each of block is a directory with chunks, one metadata file, and one index file
* On average each sample takes no more than 2 bytes
* Almost every section starts with a length field, and ends with a CRC32 checksum

### Index file
* Symbol table: a sorted list of strings, which are used in label pairs
* Series (more below): a list of series. Each series hold the label set of the series and its chunks (more below) with blocks
* List of postings: Each posting is a list of series references that contain a given label pair
* Posting offset table: list of entries, each is a label pair to offset to its series list in the postings list section. This section is sorted
* Table of content: entry point to the entire index and points to various sections in the file. 

### Series section in the index file
* Each series holds number of lables, followed by the tuple of symbol table references
* The next section is the number of indexed chunks, each of chunk index contains the min time and max time of the time, and their position in the chunk file. Note that all ts except the first one is stored as deltas


### chuck
max 512 MB, each record of chuck is len + encoding + data + CRC

### WAL
128MB by default. A segment is written to in pages of 32KB. Mostly borrowed the idea from rocksdb
* type
* len
* CRC
* data

WAL accepts
* Series records encode the labels that identifies a series and its unique ID
* Sample records encode samples as a list of triples (series_id, timestamp, value)
* Tombstone records encode tombstones as a list of triples (series_id, min_time, max_time)

### Example
Suppose i have one serive deployed on two machines, each will output [{service: sid}, {node: nid}] label sets

Inside the index file

* Symbol table:  [n1, n2, node, service, sid]
* Series: 
  * [2, 0, 3, 4]
  * min time in c1, delta for max in c1, offset in c1
  * [2, 1, 3, 4]
  * min time in c2, delta for max in c2, offset in c2
* Postings:
  * [0], [0, 1], [1] 
* Posting offset table:
  * ((node, n1), 0), ((node, n2), 2), ((service, s1), 1)

Note in the design, we have direct index on only single lable pair, or the raw data. This means most queries, we will have to merge to find series entries, and then scatter-merge

