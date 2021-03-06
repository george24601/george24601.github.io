---
layout: post
title: "System design: search engine"
description: ""
category: 
tags: [interview]
---

### Crawler
* Spider crawls web to create build data (BD)
* Estimate 1 T new page generated per day, e.g., 10 pages for everyone in the owlrd
  * Each 100K is 100 PB per day
* Need to store:
 * URL - which can be in a trie to reduce the char space
 * last_visted_at
 * hash
 * Links to crawl, separately from the crawled links
* May Separate day db, hour data, month db, change only the hour db, and divide & conquer the search result
* Seed URLs can be populated by hand
* DNS caching needs to be tuned or enchanced so since it discovers a lot new URLs

### How much can a trie save?

* assume all URLs are of lenght up to l, evenly distributed chars. So the total space is 26 + 26^2 + ... = O(l * 26^l) 
* In a trie, we have 26 chars at each level, so it is 26^l => so we saved a factor of l
* The intuition being that no matter which way we estimate, we need space to keep track only the unique marker

### Page indexer
* Indexer reads/receives URL from the crawler, and build inverted index by visiting the URL
* Child links from that page is needed for page rank 
* Choose to snapshot the page visited, including the link and snippets. This will power the document service when the search indexer returns the URLs
  * may build the document a forward index of key words and prefixes
  * for quick filtering, can use a bloomfilter for the key words and prefixes

### Search indexer
* Analyze the search keyword

### Search flow
* Goes through search indexer to limit the documents to search
* Ranker will score and sort results and return the first page result
* Skiplist to the most common appraoch for ordered LL union problem. Log(n) perf.
* Cache popular search results. Reading 1MB sequentially is 250 microsec from memory, 1ms on SSD

