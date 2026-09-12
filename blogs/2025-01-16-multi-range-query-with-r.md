---
title: "Multi-range query with R"
url: "https://forum.tiledb.com/t/multi-range-query-with-r/764#post_2"
date: "2025-01-16"
author: "@julio514 Julien Tremblay"
feed_url: "https://forum.tiledb.com/posts.rss"
---
Okay I managed to figure it out. I need to specify a matrix as range. For instance if I want indexes 3 and 6 in my example, I would need to build a query matrix like this one: > query_mat query_mat [,1] [,2] [1,] 3 3 [2,] 6 6 Then, tmp = matrix_tdb[query_mat,] But using R for querying my tiledb is really much slower than when using python.
