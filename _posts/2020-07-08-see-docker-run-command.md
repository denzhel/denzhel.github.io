---
layout: post
title: What was the original run command for that Docker container
date:   2020-07-08
categories: Docker
---
While playing with Docker containers, sometimes you forget the original command that was used to run a specific container.

You can use the following command to get that command:
```shell
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock assaflavie/runlike <YOUR CONTAINER NAME>
```
