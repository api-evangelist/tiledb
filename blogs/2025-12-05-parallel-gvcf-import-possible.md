---
title: "Parallel gVCF import, possible?"
url: "https://forum.tiledb.com/t/parallel-gvcf-import-possible/816#post_5"
date: "2025-12-05"
author: "@michaeljon Michaeljon Miller"
feed_url: "https://forum.tiledb.com/posts.rss"
---
Didn’t realize sm.mem.total_budget was in bytes. Either way, I’ve adjusted that but still running into OOM issues. I have a store with 250 samples, loaded in batches of 5x10 (50 into tiledbvcf store with a batch size of 10).
