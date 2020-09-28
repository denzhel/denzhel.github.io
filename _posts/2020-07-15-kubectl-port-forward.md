---
layout: post
title: Port forward to the pods
date:   2020-07-15
categories: Kubernetes
---
To access resources locally and forward the traffic to the pods on the Kubernetes cluster, you can use:
```shell
kubectl port-forward some-mongodb-pod-b8x9g 27017
```

You can also use a project called:
```shell
kubefwd
https://github.com/txn2/kubefwd
```

That basically allows you to forward an entire namespace and use the original pod names:
```shell
sudo kubefwd svc -n the-project
```

Then, you can access:
```shell
curl elasticsearch:9200
```
