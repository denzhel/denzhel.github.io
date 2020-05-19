---
layout: post
title: Get EC2 Instance's Name
date:   2020-05-19
categories: Amazon
---
From time to time, we are asked by Amazon to stop and start instances for their maintenance work.

This script will help you to get the name of the instance by querying the tag Name.

```bash
#!/bin/bash                                                                                                                                                                       
# A script that returns the name of the AWS ec2 instance name

# Declare variables
instance_id="$1"
profile_name="someProfile"
region="someRegion"

# Validate that an instance was provided 
if [ ${instance_id} ]; then
        echo $(aws ec2 --region "${region}" --profile "${profile_name}" describe-instances --instance-ids "${instance_id}" --query "Reservations[*].Instances[*].Tags[?Key=='Name'].Value" --output text)
else
        echo "Please provide an instance id"
fi
```
