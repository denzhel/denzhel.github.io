---
layout: post
title: Parse multiline logs with Filebeat correctly
date:   2020-06-12
categories: Elasticsearch
---
Although Elasticsearch can parse multiline messages, sometimes it can be buggy.

What I did to solve multiple crashes and errprs were to let Filebeat do all the work.

You can use the following configurations to catch your multiline logs:
```shell
multiline.pattern: '(Error:)|(^\s)'
multiline.negate: true
multiline.match: after
```

```shell
multiline.pattern: '^\s'
multiline.negate: true
multiline.match: after
```

```shell
multiline.pattern: '^[0-9]'
multiline.negate: true
multiline.match: after
```

```shell
multiline.pattern: '^\['
multiline.negate: true
multiline.match: after
```
