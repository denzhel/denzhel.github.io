---
layout: post
title: Blank page after each aws cli command
date:   2020-07-27
categories: Amazon
---
When using aws cli in a script, it can sometimes be troublesome since of the pager page.

The pager page is basically a new shell window that will appear with the data you've asked for instead of in a new line.

To fix this behaviour you can use the following command:
```shell
AWS_PAGER="" aws --profile someProfile ec2 describe-security-groups --filters Name=group-name,Values=*test*
```
