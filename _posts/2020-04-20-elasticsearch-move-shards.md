---
layout: post
title: Move shards between Elasticsearch nodes
date:   2020-04-20
categories: Elasticsearch
---

Since one of our nodes used most of its disk space, we needed to move some shards to other nodes.

List all the nodes on your cluster:
```
curl -X GET "localhost:9200/_cat/nodes"
```

Retreive the node id
``` 
curl -X GET "localhost:9200/_nodes/NodeName/id?pretty"

{
  "cluster_name" : "elasticsearch",
  "nodes" : {
    "7wkfBrYbT9KgErV8_geseg" : {   <========= Node ID
      "name" : "prod-elasticsearch-12",
      "transport_address" : "xxxxxxxxxxxx",
      "host" : "prod-elasticsearch-12",
      "ip" : "xxxxxxxx",
      "version" : "xxxxxx",
      "build" : "xxxxx",
      "http_address" : "xxxxxxx",
      "attributes" : {
        "max_local_storage_nodes" : "xx",
        "rack" : "xxxx", 
        "master" : "xxxx"
      } 
    }
  }
}
```

Find a big index:
```
curl -X GET 'localhost:9200/_cat/indices?v&s=store.size:desc'
```

Make sure it is located on the overloaded server:
```
curl -X GET 'localhost:9200/_cat/shards/IndexName?v'

index                    shard prirep state      docs  store ip        node                  
IndexName$2018_3 0     p      STARTED 7998483 17.1gb 127.0.0.1 prod-elasticsearch-19 
IndexName$2018_3 0     r      STARTED 7998483 17.1gb 127.0.0.1 prod-elasticsearch-11 
IndexName$2018_3 1     p      STARTED 8000627 17.1gb 127.0.0.1 prod-elasticsearch-16 
IndexName$2018_3 1     r      STARTED 8000627 17.1gb 127.0.0.1 prod-elasticsearch-12  <==

```

Use the commands above to choose a destination node.

Move the replica shard 1 to the chosen server:
```
curl -X POST -H "Content-Type: application/json" 'http://localhost:9200/_cluster/reroute' -d '{
    "commands" : [ {
  "move" : {
   "index" : "IndexName$2018_3", "shard" : 1, "from_node" : "7wkfBrYbT9KgErV8_geseg", "to_node" : "V9xn31PsSwSlq31OTsCEqg"
  }
 } ]
}'
```

Monitor the cluster status:
```
curl -X GET 'localhost:9200/_cluster/health?pretty'

{
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 9,
  "number_of_data_nodes" : 9,
  "active_primary_shards" : 5789,
  "active_shards" : 9703,
  "relocating_shards" : 1,     <==== Monitor this 
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0
}
```

