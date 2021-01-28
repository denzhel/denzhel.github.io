---
layout: post
title: Query elasticsearch for disk usage
date:   2021-01-28
categories: Elasticsearch
---
If you want to query your elasticsearch cluser for disk usage, use this:
```bash
curl -XGET "http://localhost:9200/_cat/allocation?v&pretty"
```

Output:
```bash
shards disk.indices disk.used disk.avail disk.total disk.percent host          ip            node
  3213      460.2gb   584.9gb      1.3tb      1.9tb           29 1.1.1.1       1.1.1.1       host1.example.com
  3213      479.5gb   599.3gb      1.3tb      1.9tb           29 1.1.1.2       1.1.1.2       host2.example.com
  3214        455gb   577.5gb      1.4tb      1.9tb           28 1.1.1.3       1.1.1.3       host3.example.com
  3214        465gb   585.6gb      1.3tb      1.9tb           29 1.1.1.4       1.1.1.4       host4.example.com
  3213      468.6gb   592.8gb      1.3tb      1.9tb           29 1.1.1.5       1.1.1.5       host5.example.com
  3213      461.9gb   582.4gb      1.3tb      1.9tb           28 1.1.1.6       1.1.1.6       host6.example.com
  3214      447.9gb   568.9gb      1.4tb      1.9tb           28 1.1.1.7       1.1.1.7       host7.example.com
```
