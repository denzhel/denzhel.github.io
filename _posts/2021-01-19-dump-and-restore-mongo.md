---
layout: post
title: Backup and Restore MongoDB
date:   2021-01-12
categories: MongoDB
---
There are many ways to backup and restore MongoDB, I found this to be the easy one.

To dump ALL of MongoDB's databases, use the following:
```bash
mongodump --archive=server.archive --gzip -j 24
```

To restore this dump:
```bash
mongorestore --archive=server.archive --gzip
```

Explanation:
* --gzip - compress the archive file
* -j 24 - dump 24 collections simultaneously
