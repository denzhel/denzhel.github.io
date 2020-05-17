---
layout: post
title: Using JSON/YAML files in Terraform
date:   2020-05-03
categories: Terraform
---

To use Terraforms parsing powers of YAML and JSON files, there are some built-in functions.

The first one is jsondecode, we can use it as follows:
```hcl
locals {
  services_file   = jsondecode(file("${path.module}/services.json"))
}
```

Use a for loop to parse the file and create a map of objects, our instances.
```hcl
instances = flatten([
    for instance_key, instance in local.services_file.services : [
      for index in range(instance.instance_count) : {
        service_name          = instance_key
        instance_type         = instance.instance_type
        instance_index        = index
        instance_root_volume  = instance.instance_root_volume
        instance_data_volumes = instance.instance_data_volumes
        instance_subnet_type  = instance.instance_subnet_type
      }
    ]
  ])
```

Our JSON file:
```json
{                                                                                                                                                                                 
  services: {
    frontend: {
      service_name: frontend,
      instance_count: 3,
      instance_type: t2.micro,
      instance_ami: amzn-ami-hvm-2018.03.0.20200206.0-x86_64-gp2,
      instance_root_volume: 50,
      instance_data_volumes: [3],
      instance_subnet_type: public
  }
 }
}
```

The second function is yamldecode:
```hcl
locals {
security_groups_file = yamldecode(file("${path.module}/../security_groups.yml"))
}
```

To loop through the data, you can use a for loop same as above.

Enjoy :)
