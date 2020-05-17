---
layout: post
title: Cluster-autoscalers upgrade broke auto-discovery
date:   2020-05-03
categories: Kubernetes
---

If you've upgraded your Kubernetes cluster-autoscaler from 0.X to 1.X and using auto-discovery
to find AWS ASGs, make sure to update the ASGs tags since they were changed.

The following resource tags on the AWSs ASG resource it self are required by the autoscaler:
```shell
k8s.io/cluster-autoscaler/enabled 
kubernetes.io/cluster/<YOUR CLUSTER NAME>
```

After the upgrade(for me it was 0.X -> 1.15), auto-discovery stopped working.

We then realized the tags were changed to:
```shell
k8s.io/cluster-autoscaler/enabled
k8s.io/cluster-autoscaler/<YOUR CLUSTER NAME>
```

Once the tags were changed, auto-discovery started discovering again.
