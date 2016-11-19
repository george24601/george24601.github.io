---
layout: post
title: "The Design and Implementation of a Log-Structured File System"
description: "Study question"
category: 
tags: [paper]
---

1. Why the paper says the disk operation will be dominated by writes?

2. What are pros and cons of a large file buffer?

3. For a small file focused workload, what is the performance characteristics?

4. Compare the inode discoery mechanisms of BFS and LFS

5. given we have dir1/file1, and dir2/file2 next to each other on our log, what will the log look like?

6.describes segment cleaning up process

7.How does LFS decide if a block is live?

8.Describe the segment usage table

9. How does LFS recover when the checkpointing op itself failed?

10. what are the benefits of having inode block AFTER data blocks?

11. How does LFS recover from the case where inode and directory info are not consistent?

12. Why LFS does not support fuzzy checkpoints, unlike ARIES and its WAL?

13. Why LFS wants a bimodal distribution of segment utilization?

14. explain the cost-benefit policy and why it helps achieve the bimodal distribution of utilization?
