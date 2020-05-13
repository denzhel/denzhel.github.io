---
layout: post
title: Query data usage across the month
date:   2020-05-13
categories: Elasticsearch
---

To query your elasticsearch cluster for data usage across the month, you can use the following script:

```sh
#!/bin/bash
# The following script calculates our elasticsearch usage across the current month

# Define date inputs
from_day=01
to_day=$(date +%d)
this_month=$(date +%m)
this_year=$(date +%Y)

# Define temp file
tmp_file="/tmp/numbers"

for day in $(eval echo "{${from_day}..${to_day}}"); do
        curl -s -X GET "localhost:9200/_cat/indices/*-${this_year}.${this_month}.${day}/?bytes=b&h=pri.store.size" > ${tmp_file}
        total=$(paste -sd+ ${tmp_file} | bc)
        if [ ${total} ]; then
                printf '%s\n' "${day}.${this_month}.${this_year} $((${total}/ 10**9))"
        fi;
done
```

You can replace:

```
pri.store.size
```
With:
```
store.size
```

To get the data usage of primary and replica shards.
