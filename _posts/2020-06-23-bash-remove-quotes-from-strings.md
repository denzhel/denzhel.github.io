---
layout: post
title: Remove quotes from a string
date:   2020-05-21
categories: Shell
---
To remove quotes from a string, use the following command:
```shell
echo "Test" | tr -d '"'
```

For example, I've used it on the following aws cli command:
```shell
aws --profile someProfile --region us-east-1 ecr describe-repositories | grep repositoryName | cut -d":" -f2 | tr -d '"' | tr -d ',' > repositories
```
