---
layout: post
title: Import existing resources the right way
date:   2020-06-27
categories: Terraform
---
When trying to import resources to a complex setup of Terraform using the following command:
```bash
terraform import module.instances.aws_instance.bp_instance["xx-supervisor-xx"] i-12344556
```

I always got the following error:
```bash
Error: Index value required

  on <import-address> line 1:
   1: module.instances.aws_instance.bp_instance[xx-supervisor-xx]

Index brackets must contain either a literal number or a literal string.

For information on valid syntax, see:
https://www.terraform.io/docs/internals/resource-addressing.html
```

That's because escaping for the instance key name is required, like this:
```bash
terraform import module.instances.aws_instance.bp_instance[\"xx-supervisor-xx\"] i-12344556
```
