---
title: "How to Handle Corrupted or Missing Fragments in TileDB with S3 Backend"
url: "https://forum.tiledb.com/t/how-to-handle-corrupted-or-missing-fragments-in-tiledb-with-s3-backend/761#post_1"
date: "2025-01-13"
author: "@Simon_Besnard Simon Besnard"
feed_url: "https://forum.tiledb.com/posts.rss"
---
Hello TileDB Community, I am currently working with TileDB arrays stored on an S3 bucket ( array_uri ) and am encountering an issue with corrupted or missing fragments causing errors when accessing the array. Here’s a detailed explanation of my setup, the issue, and how I write data: Problem Description When attempting to write or access my TileDB array stored in S3, I encounter the following error: TileDBError: [TileDB::S3] Error: Cannot retrieve S3 object size; Error while listing file s3://dog.gedidb.gedi-l2-l4-v002/array_uri/__fragments/__1736465921075_1736465921075_62934a75c55ee3114948ab1
