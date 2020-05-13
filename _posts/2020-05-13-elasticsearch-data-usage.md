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
	# Get the data usage only for the primary shard
        curl -s -X GET "localhost:9200/_cat/indices/*-${this_year}.${this_month}.${day}/?bytes=b&h=pri.store.size" > ${tmp_file}
        total=$(paste -sd+ ${tmp_file} | bc)
	# Check if the total variable is not empty, that way only days with data will be printed
        if [ ${total} ]; then
		# Output the full date of the day and the data usage in GB
                printf '%s\n' "${day}.${this_month}.${this_year} $((${total}/ 10**9))"
        fi;
done
```
Output:
```sh
01.05.2020 93
02.05.2020 110
03.05.2020 96
04.05.2020 94
05.05.2020 93
06.05.2020 94
07.05.2020 100
08.05.2020 98
09.05.2020 96
10.05.2020 93
11.05.2020 95
12.05.2020 107
13.05.2020 90
```

You can replace:

```sh
pri.store.size
```
With:
```sh
store.size
```

To get the data usage of primary and replica shards.
