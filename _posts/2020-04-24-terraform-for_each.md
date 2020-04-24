---
layout: post
title: Use Terraforms for_each the right way
date:   2020-04-24
categories: Terraform
---

Terraform has its unique logic and way of dealing with things, most of the time causes you to pull your hair out.

Learning Terraform was and still is a challenging task for me, I've learned a lot and I want to share a tip.

When I've just started looping through things, I used count and then realized for_each is better. One of the main reasons it's better, 
is the ability to use a unique key for each resource created. I will explain.

In the following example, I use aws_security_group_rule resource to add rules to a security group. I have a YAML file that contains
the name of the group(in this example: test) and blocks of which port to open and to which group.

```hcl
resource "aws_security_group_rule" "bp_service_sg_rule" {                                                                                                     
  for_each          = { for index, rules in local.security_group_rules : index => rules }
  type              = "ingress"
  from_port         = each.value.open_port
  to_port           = each.value.open_port
  protocol          = "tcp"
  security_group_id = "sg-0e5e36abc940a1a2a"
  cidr_blocks       = [each.value.to_group]
}
```

```hcl
test:      <===== The name of the security group                                                                                                                          
- open_port: 22   <===== The port number that I want to open   
  to_group: 4.4.4.4/32    <===== The ip address I want to open it to.
```

As time passes by, I need to update the file with various security groups and rules. The problem starts here.

```hcl
test:
- open_port: 22             <====== The new
  to_group: 3.3.3.3/32    <======    rule
- open_port: 22                                                                                                                                             
  to_group: 4.4.4.4/32
```

Our logic tells us to always append the new rules to the end of the file, but after all, we are humans.
As soon as you append your new rules before the existing ones and run "terraform apply", the following happens:

```hcl
  # aws_security_group_rule.bp_service_sg_rule["0"] must be replaced
-/+ resource "aws_security_group_rule" "bp_service_sg_rule" {
      ~ cidr_blocks              = [ # forces replacement
          - "4.4.4.4/32",
          + "3.3.3.3/32",
        ]
        from_port                = 22
      ~ id                       = "sgrule-3857769040" -> (known after apply)
      - ipv6_cidr_blocks         = [] -> null
      - prefix_list_ids          = [] -> null
        protocol                 = "tcp"
        security_group_id        = "sg-0e5e36abc940a1a2a"
        self                     = false
      + source_security_group_id = (known after apply)
        to_port                  = 22
        type                     = "ingress"
    }

  # aws_security_group_rule.bp_service_sg_rule["1"] will be created
  + resource "aws_security_group_rule" "bp_service_sg_rule" {
      + cidr_blocks              = [
          + "4.4.4.4/32",
        ]
      + from_port                = 22
      + id                       = (known after apply)
      + protocol                 = "tcp"
      + security_group_id        = "sg-0e5e36abc940a1a2a"
      + self                     = false
      + source_security_group_id = (known after apply)
      + to_port                  = 22
      + type                     = "ingress"
    }
```

Since the rules index changed, Terraform will now destroy all the rules and recreates them - meaning HUGE down time to production.
You ask why ? Well .. the way I've configured for_each.

The current for_each uses the rules index as key, meaning it can be changed according to the position in the YAML file - BAD.

To overcome this issue, I needed to assemble a unique key:

```hcl
for_each = { for rules in local.security_group_rules : "sg:${rules.service_name}||allow_port:${rules.open_port}||from:${rules.to_group}" => rules }
``` 

Now, all the resources have a unique key:

```hcl
aws_security_group_rule.bp_service_sg_rule["sg:kafka||allow_port:telegraf||from:grafana"]
```

Problem's solved !
