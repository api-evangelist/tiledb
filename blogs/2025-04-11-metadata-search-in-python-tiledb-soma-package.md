---
title: "Metadata Search in Python TileDB SOMA Package"
url: "https://forum.tiledb.com/t/metadata-search-in-python-tiledb-soma-package/788#post_2"
date: "2025-04-11"
author: "@spencerseale Spencer Seale"
feed_url: "https://forum.tiledb.com/posts.rss"
---
tiledbsoma.Experiment objects are abstractions of tiledb.Group s, so you can use tiledb-py to open up the experiment as a group object and look at its metadata: import tiledb with tiledb.Group(soma_uri) as grp: print(grp.meta) In tiledbsoma you follow a similar pattern: with tiledbsoma.open(soma_uri) as soma_exp: print(soma_exp.metadata) For users of our cloud platform, you can query all asset metadata ultra-fast using our python REST api: import tiledb.cloud response = tiledb.cloud.client.list_groups( namespace, group_type="soma", per_page=500, search="T cell", with_metadata=True, )
