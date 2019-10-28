---
layout: post
title: "System design: search engine"
description: ""
category: 
tags: [interview]
---

* Spider crawls web to create build data (BD)
* Search data (SD) + BD to build index
  * Build index will generate the inversed index
  * Search index will analyze the search keywaord, and filter it roughly by the search idnex
* Rank will score and sort results and return the first page result. may parallize bucket search by partitioning by ranges
* Skiplist to the most common appraoch for ordered LL union problem. Log(n) perf.
* Separate day db, hour data, month db, change only the hour db, and divide & conquer the search result

