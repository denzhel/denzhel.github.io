---
layout: post
title: Check Elasticsearch JVM usage
date:   2020-05-30
categories: Elasticsearch
---
If you've stumbled upon the following error:
```bash
May 26 10:03:31 prod-xxx kernel: [17974674.639265] elasticsearch[p invoked oom-killer: gfp_mask=0x280da, order=0, oom_score_adj=0
```

Make sure that Elasticsearch is not using your whole memory by:
```bash
free -m
```

In addition, check that Elasticsearch is not using the entire heap size:
```bash
curl -s -X GET https://localhost:9200/_nodes/<NodeID>/stats?pretty | grep heap_used_percent
```

You can get the Node's ID by using:
```bash
curl -s -X GET https://localhost:9200/_nodes?pretty
```
