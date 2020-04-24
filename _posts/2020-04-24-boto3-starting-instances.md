---
layout: post
title: Boto3 exception when starting AWS instances
date:   2020-04-24
categories: Python
---

Recently we've began taking various steps to reduce our payment to Amazon by turning off all instances
in our testing environment.

There are many ways to do that, including CloudWatch. I chose the code way and I've stumbled upon the following
error when trying to power on all of the 200 instances at once:
```
botocore.exceptions.ClientError: An error occurred (InsufficientInstanceCapacity) when calling the StartInstances operation (reached max retries: 4): Insufficient capacity.
```

Basically, the issue is that Amazon cannot allocate that many on demand machines for you. 

The solution is to break the list into small lists and start them chunk by chunk:

```python
    # The function that I use to break the list to smaller chunks
    def chunks(list_to_chunk, chunk_by):
        for i in range(0, len(list_to_chunk), chunk_by):
            yield list_to_chunk[i:i + chunk_by]

    # The loop I use for starting the chunks one by one
    # Where 14 is the chunk size I chose to be on the safe size 200/14 = 14 machines in each chunk
    # get_ids() is the function I use to retrieve the list
    for chunk_group in list(chunks(get_ids(), 14)):
        ec2.start_instances(InstanceIds=chunk_group, DryRun=test_mode)
```
