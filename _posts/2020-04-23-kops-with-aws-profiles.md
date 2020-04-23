---
layout: post
title: Kops with AWS profiles
date:   2020-04-23
categories: Kubernetes
---

In order to configure/ edit/ maintain your Kubernetes cluster using kops, you need credentials.
These credentials are the same as the ones you use to interact with your AWS account.

Sometimes, you have multiple accounts and/or mutiple users with different permissions, different keys and secret keys.
Personally, I avoid setting these sensitive keys as environment variables by using the following method.

```
Kops - Version 1.16.0
AWS - aws-cli/2.0.8 Python/3.8.2 Darwin/19.4.0 botocore/2.0.0dev12
```

First, update your aws configuration:

```
vi ~/.aws/config

[profile kops_admin]
region=XX-XXXX-X
```

```
vi ~/.aws/credentials

[kops_admin]
aws_access_key_id=XXXXXXXXX
aws_secret_access_key=XXXXXXXXXX
```

Add an alias to your bash/zsh profile:

```
vi ~/.zshrc

alias kops="AWS_ACCESS_KEY_ID=$(aws configure get profile.kops_admin.aws_access_key_id) AWS_SECRET_ACCESS_KEY=$(aws configure get profile.kops_admin.aws_secret_access_key) kops"
```

Now, you can use kops without setting anything beforehand 

Bonus - to avoid setting the backend store each time, you can use kops configuration file (I use Amazons S3)

```
vi ~/.kops/config

kops_state_store: s3://<BucketName/ClusterName>
```
