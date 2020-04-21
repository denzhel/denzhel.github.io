---
layout: post
title: Disable shard allocation on Elasticsearch
date:   2020-04-21
categories: Elasticsearch
---

When you want to do some kind of a maintenance task on your Elasticsearch nodes,
it is best to disable shard allocation. This way the node will not process any new shards.


To disable shard allocation on Elasticsearch 1.7.X:
```
curl -XPUT -H "Content-Type: application/json" 'http://localhost:9200/_cluster/settings' -d '{
    "transient" : {
        "cluster.routing.allocation.disable_allocation": "true"
    }
}'


curl -XPUT 'http://localhost:9200/_cluster/settings' -d '{
    "transient" : {
        "cluster.routing.allocation.enable": "none"
    }
}'
```

To disable shard allocation on Elasticsearch 6.8.X:
``` 
curl -X PUT "http://localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d '{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}'
```

After the maintantance work, it is time to enable the shard allocation.

To enable shard allocation on Elasticsearch 1.7.X:
```
curl -XPUT -H "Content-Type: application/json" 'http://localhost:9200/_cluster/settings' -d '{
    "transient" : {
        "cluster.routing.allocation.disable_allocation": "false"
    }
}'


curl -XPUT 'http://localhost:9200/_cluster/settings' -d '{
    "transient" : {
        "cluster.routing.allocation.enable": "all"
    }
}'
```

To disable shard allocation on Elasticsearch 6.8.X:
```
curl -X PUT "http://localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d '{
  "persistent": {
    "cluster.routing.allocation.enable": "all"
  }
}'
```
