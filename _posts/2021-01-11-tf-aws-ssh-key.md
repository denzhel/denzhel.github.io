---
layout: post
title: Create AWS SSH Key through Terraform
date:   2021-01-11
categories: Terraform
---
To create a SSH key pair for EC2 instance/ EKS workers, use the following methods.

First, create a local SSH public/ private keys:
```bash
ssh-keygen -f ~/.ssh/<KeyName> -t rsa -b 4096
```

Upload it to AWS Secret Manager:
```bash
aws secretsmanager create-secret \
--name "<PathToSecretName>" \
--description "Added Manually" \
--secret-string "$(cat <KeyName>.pub)"
```

Use this Terraform block to get the secret:
```HCL
data "aws_secretsmanager_secret_version" "<SecretName>" {
  secret_id = "<PathToSecretName>"
}

resource "aws_key_pair" "<KeyPairName>" {
  key_name   = "<KeyPairName>"
  public_key = data.aws_secretsmanager_secret_version.<SecretName>.secret_string
  tags = {
    Terraform   = "true"
    Environment = terraform.workspace
  }
}
```

Finally, use it inside the ec2_instance module:
```HCL
resource "aws_instance" "<InstanceName>" {
  ...
  key_name = aws_key_pair.atlas_es_data_key_pair[0].key_name
  ...
}
```
