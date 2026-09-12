---
title: "Tile capacity when capacity = 0 in Python API?"
url: "https://forum.tiledb.com/t/tile-capacity-when-capacity-0-in-python-api/821#post_2"
date: "2026-02-06"
author: "@spencerseale Spencer Seale"
feed_url: "https://forum.tiledb.com/posts.rss"
---
Hi, I recommend you check out the docs here: TileDB When creating an array schema, you should consider the query patterns for that data and the typical slice that will be made on the data. For verticals like TileDB-SOMA, we standardize many of these low-level parameters. With TileDB-Py and the wide range of use cases, we leave it up to the user to spec the array parameters of their choosing.
