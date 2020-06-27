---
layout: post
title: AWS ECR Lifecycle Policy
date:   2020-06-27
categories: Terraform
---

There are numerous ways of applying a life cycle policy to an ECR, I want to show one of them.

We manage the ECR list in a yml file:
```yml
- { name: "infra/alertmanager", expiration_days: 14, expiration_count: 30}
```

We import the file using yaml decode function:
```hcl
repositories_file      = yamldecode(file("${path.module}/lists/ecr_repositories.list"))
```

Since our setup uses modules, we pass the list as a variable:
```hcl
module "aws_ecr" {
  source           = "./modules/aws/ecr"
  ecr_repositories = local.repositories_file
}
```

Then we use this block of code to loop through the list and apply the inline policy WITH variables:
```hcl
resource "aws_ecr_lifecycle_policy" "bp_ecr_repo_lifecycle_policy" {
  for_each = { for repo in var.ecr_repositories.ecr_repositories : repo.name =>
  { expiration_days = repo.expiration_days, expiration_count = repo.expiration_count } }
  repository = each.key
  policy     = <<EOF
{
    "rules": [
        {
            "rulePriority": 1,
            "description": "Expire images that are older than ${each.value.expiration_days} days",
            "selection": {
                "tagStatus": "tagged",
                "tagPrefixList": ["branch-"],
                "countType": "sinceImagePushed",
                "countUnit": "days",
                "countNumber": ${each.value.expiration_days}
            },
            "action": {
                "type": "expire"
            }
        },
        {
            "rulePriority": 2,
            "description": "Expire images when the amount is bigger than ${each.value.expiration_count}",
            "selection": {
                "tagStatus": "tagged",
                "tagPrefixList": ["v"],
                "countType": "imageCountMoreThan",
                "countNumber": ${each.value.expiration_count}
            },
            "action": {
                "type": "expire"
            }
        }
    ]
}
EOF
}
```

Notice that:
```hcl
for_each = { for repo in var.ecr_repositories.ecr_repositories : repo.name =>
    { expiration_days = repo.expiration_days, expiration_count = repo.expiration_count } }
```
For_each function builds the key value, the key being the repo name and the values are the ${expiration_days} and ${expiration_count} variables.
