---
layout: post
title: Error while pulling a parameter using Node from AWS Parameter Store
date:   2020-06-12
categories: Amazon
---
We have a Node applications that pulls encrypted parameters from AWS Parameter Store.

I've attached a role to the EC2 Instance and expected it to work out of the box:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ssm:GetParameters",
                "ssm:GetParameter"
            ],
            "Resource": "*"
        }
    ]
}
```

When trying to actually pull the secret, we got this error:
```shell
message: 'The ciphertext refers to a customer master key that does not exist, does not exist in this region, or you are not allowed to access. (Service: AWSKMS; Status Code: 400; Error Code: AccessDeniedException; Request ID: 687bb97b-3c47-4684-9c48-a9491604ddc7; Proxy: null)',
  code: 'AccessDeniedException',
  time: 2020-06-09T10:23:43.047Z,
  requestId: 'e746b787-c1ac-40b8-b697-614b81ce77f8',
  statusCode: 400,
  retryable: false,
  retryDelay: 43.327480386312914
```

Eventually, I found that I had a permission missing "Decrypt":

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
                "ssm:GetParameters",
                "ssm:GetParameter"
            ],
            "Resource": "*"
        }
    ]
}
```

Problem is Fixed.
