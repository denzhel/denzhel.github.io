---
layout: post
title: Conditional Terraform for_each
date:   2020-05-30
categories: Terraform
---
Sometimes, you want to use a resource conditionally:
```bash
resource "aws_iam_policy" "bp_iam_policy" {
  for_each = var.is ? local.policies_file.policies : {}
  name   = each.key
  path   = "/"
  policy = jsonencode(each.value)
}
```
Meaning: if var.is equals True, the true exporession will provide for_each to local.polocies_file.
If var.is equals False then provide an empty object.

For 90 precent of the cases, this syntax will work and for the other 10 precent you will get:
```bash
Error: Inconsistent conditional result types
  on modules/aws/iam/iam_policies/main.tf line 4, in resource "aws_iam_policy" "iam_policy":
   4:   for_each = var.create_iam_resources ? { for name, info in local.policies_file.policies: name => info } : {}
    |----------------
    | local.policies_file.policies is object with 2 attributes
The true and false result expressions must have consistent types. The given
expressions are object and object, respectively.
```

The workaround for this is to use try function:
```bash
resource "aws_iam_policy" "bp_iam_policy" {
  for_each = try(var.is ? local.policies_file.policies : tomap(false), {})
  name   = each.key
  path   = "/"
  policy = jsonencode(each.value)
}
```
