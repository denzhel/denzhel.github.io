---
layout: post
title: "Elasticsearch: file does not contain valid certificates"
date:   2020-05-08
categories: Elasticsearch
---

Recently, we've deployed new Logstash instances and encountered the following error:

```
Pipeline aborted due to error {:pipeline_id=>"main", :exception=>java.lang.IllegalArgumentException: File does not contain valid certificates
```

Funny as it may sound, we've realized that there was no issue with the certificate it self, rather its permissions.

What did the trick was letting users other root access it:


```
chmod o+r cert.crt
chmod o+r cert.key
```
