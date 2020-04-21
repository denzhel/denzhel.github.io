---
layout: post
title: Remove a node from an elasticsearch cluster
date:   2020-04-21
categories: Elasticsearch
---

To remove a node from an elasticsearch cluster, first get its IP address:
```
curl -X GET "localhost:9200/_nodes/NodeName/id?pretty"

{
  "cluster_name" : "elasticsearch",
  "nodes" : {
    "xxxxxxxxxxx" : {   
      "name" : "xxxxxxxxxx",
      "transport_address" : "xxxxxxxxx",
      "host" : "xxxxxxxxx",
      "ip" : "1.1.1.1", <=== IP Address
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
Exclude the node from shard allocation:
```
curl -XPUT localhost:9200/_cluster/settings?pretty -H 'Content-Type: application/json' -d '{
  "transient" :{
      "cluster.routing.allocation.exclude._ip" : "1.1.1.1"
   }
}';
```
Wait for the cluster to finish reallocate the shards(until it becomes 0):
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
  "relocating_shards" : 301,     <==== Monitor this 
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0
}
```
Shut down the node and remove the exclusion:
```
curl -XPUT localhost:9200/_cluster/settings?pretty -H 'Content-Type: application/json' -d 
'{
  "transient" :{
      "cluster.routing.allocation.exclude._ip" : ""
   }
}'
```
