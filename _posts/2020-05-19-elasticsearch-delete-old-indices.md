---
layout: post
title: Delete old Elasticsearch indices
date:   2020-05-19
categories: Elasticsearch
---
Since Elasticsearch doesn't offer a machanism that deletes old indices, Curator is here for the rescue.

Curator can perform alot of tasks, one of them is delete old indices.

In my case, I had to delete indices older than 14 days:

```shell
/usr/local/bin/curator_cli delete_indices --ignore_empty_list --filter_list '[{"filtertype":"age","source":"creation_date","direction":"older","unit":"days","unit_count":14},{"filtertype":"pattern","kind":"prefix","value":"logstash"}]'
```

If you want to run it nightly, simply add it to crontab and use this cron syntax:
```bash
0 1 * * * <Command>
```

You can also keep track of the deleted indices by adding this to the end of the command:
```bash
 > /var/log/elasticsearch/cron_delete_indices 2>&1
```
