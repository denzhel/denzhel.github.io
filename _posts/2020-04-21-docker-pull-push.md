---
layout: post
title: Docker: pull, modify and push
date: 2020-04-21
categories: docker
---

Sometimes, we need to pull an official/non official images, modify them and push them to our image repository.
To do that, we can use the following commands.

First, pull the docker image you want to modify:
```
docker pull elasticsearch:5.3.2
```

Create a container out of the pulled image:
```
docker container run -itd --name es532 elasticsearch:5.3.2
```

Check that the container started properly and copy the container's id:
```
sudo docker ps

CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                NAMES
c237912e7086        elasticsearch:5.3.2   "/docker-entrypoint.…"   17 seconds ago      Up 16 seconds       9200/tcp, 9300/tcp   es532
```

Make the modifications you want, I'll show some of them:
```
sudo docker exec -it -u root <ContainerID> /bin/bash
sudo docker cp . <ContainerID>:/usr/share/elasticsearch/config
```

You can make a direct change to the CMD or ENTRYPOINT and create a new image:
```
docker commit --change='CMD ["elasticsearch"]' <ContainerID> <NewImageName>
```

If you didn't make a direct change to CMD or ENTRYPOINT, create a new image out of the modified container:
```
docker container commit <modifiedContainerID> <NewImageName>
```

Tag the image with the repository address:
```
docker tag <NewImageName>:latest <RepositoryAddress e.g 123456789.dkr.ecr.us-east-2.amazonaws.com/companyName/elasticsearch5.3.2> 
```

Login to your repository before pushing:
```
aws ecr get-login-password --region us-east-2 \
| docker login --username AWS --password-stdin \ 
123456789.dkr.ecr.us-east-2.amazonaws.com
```

Push the image to your repository:
```
docker push 123456789.dkr.ecr.us-east-2.amazonaws.com/companyName/elasticsearch5.3.2 
```
