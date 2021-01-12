---
layout: post
title: Set number of replicas for index/indices
date:   2021-01-12
categories: Elasticsearch
---
To change the number of replicas for a an index, you can use the following one liner command:
```bash
curl -s -X PUT "localhost:9200/<IndexName>/_settings" -H 'Content-Type: application/json' -d' { "index" : { "number_of_replicas" : 1 } }'
```

You can also use a for loop to go over a list:
```bash
curl -s localhost:9200/_cat/indices?pretty | cut -d" " -f4 > all_indices

vi all_indices      <==== If you need to pull some indices out

for i in $(cat all_indices); do curl -s -X PUT "localhost:9200/$i/_settings" -H 'Content-Type: application/json' -d' { "index" : { "number_of_replicas" : 1 } }'; done
```
