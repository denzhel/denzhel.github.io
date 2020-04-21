---
layout: post
title: List pods on specific Kube node
date:   2020-04-21
categories: Kubernetes
---

To list pods that runs on a specific Kubernetes node: 

```
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=NodeName
```
