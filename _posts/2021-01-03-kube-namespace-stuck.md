---
layout: post
title: K8s namespace is stuck at deleting
date:   2021-01-03
categories: Kubernetes
---
If you're trying to delete a namespace and you are stuck with this error for too long:
```
Error from server (Conflict): Operation cannot be fulfilled on namespaces "aqua-test": The system is ensuring all content is removed from this namespace.  Upon completion, this namespace will automatically be purged by the system.
```

Enter the following commands to fix it:
```
kubectl patch ns <NamespaceName> -p '{"metadata":{"finalizers":null}}';
```

Finally delete the namespace:
```
kubectl delete ns <NamespaceName>
````
