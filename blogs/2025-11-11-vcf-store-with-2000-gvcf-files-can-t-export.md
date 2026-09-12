---
title: "VCF store with ~2000 gVCF files, can't export"
url: "https://forum.tiledb.com/t/vcf-store-with-2000-gvcf-files-cant-export/815#post_1"
date: "2025-11-11"
author: "@Michaeljon_Miller Michaeljon Miller"
feed_url: "https://forum.tiledb.com/posts.rss"
---
Before turning the keys over to our researchers, who I know are know are going to try to export data to multi-sample gVCF, I thought I’d try something simple. So I imported a subset of our gVCF data into a bucket on S3. If I run tiledbvcf list on that bucket I eventually, like 5 minutes, get a list of sample names back.
