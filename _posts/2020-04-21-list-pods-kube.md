---
layout: post
title: List pods on specific Kube node
date:   2020-04-21
categories: Kubernetes
---

To list pods that runs on a specific Kubernetes node: 
```shell
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=NodeName
```

To list all pods in the current namespace and they nodes they are running on:
```shell
kubectl get pods -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name
```
