---
title: "Importing parquet file as a 2d dense array"
url: "https://forum.tiledb.com/t/importing-parquet-file-as-a-2d-dense-array/758#post_3"
date: "2025-01-15"
author: "@spencerseale Spencer Seale"
feed_url: "https://forum.tiledb.com/posts.rss"
---
HI @yoshi , Thanks for the question and sorry for the delay! For writing large files that won’t fit into memory, I recommend you first create the array. During array creation, you’ll define the schema exactly as you’d like such as defining a single attribute.
