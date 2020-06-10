---
title: "Docker Tutorial"
date: 2020-06-10T13:44:54-04:00
---

# Short Tutorial on Docker

### **Why Docker?**

By now Docker has been around for quite a few years, and has made it's advantages and importance visible to the Development-Operations(DevOps) world.

Virtual Machine images are huge in size as they contain the Guest OS required for the application to run. Whereas Container images are pretty lightweight as they may not contain a Guest OS inside and can run off the Host OS, thus containing only the Application to be run and it's dependencies(Libraries and Binaries). 
This becomes extremely beneficial when the deployments are done on a huge scale.

With Docker the various teams need not worry about preparing their platform to run/test their applications, as the applications' containers would already be packaged with it's dependencies. Therefore this minimalistic approach of Docker containers help in speeding up the process of Development, Testing & Deployment.

## **Lets begin with the Docker tutorial now**

### **Docker Registries:**

There already exists a huge collection of Docker images built by various organizations and individuals, that you can use and build upon. These images are hosted on various registries. The most popular one of them is the [DockerHub](https://hub.docker.com/).

There are others as well such as:
[Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/#overview)
[Amazon Elastic Container Registry](https://aws.amazon.com/ecr/)
[Google Container Registry](https://cloud.google.com/container-registry)

### **Docker Images vs Containers:**

We started off this tutorial by hearing about Docker Containers and now we are talking about Docker Images. So what is the difference between the two? If you have used Virtual Machines in the past you must have heard of Snapshots. So Containers can be thought of snapshots Docker Images in the runtime. But you still can't make any changes to the Docker Images, that needs to be done before the Docker Image has been built.

### **What is inside a Docker Image:**

Basically, anything and everything that the application needs to run, that includes obviously the Application code, any libraries or binaries, any environment variables that need to be set or any kind of miscellaneous files.

### **Installing Docker and Docker-compose:**

Installing Docker and Docker-compose is pretty straightforward. [Installing Docker](https://docs.docker.com/get-docker/). [Installing Docker-compose](https://docs.docker.com/compose/install/)

### **Running a container**

You can search for existing images on DockerHub using the command:  `docker search redis`  
You can then run the image with the following way `docker run -d redis -p 6379:6379 --name redis-db`. If there is no specific version mentioned in the command Docker downlaods and runs the latest version of the image.  
The `-d`  flag makes the container run it *detached* mode i.e. in backgroud. If it's an interactive application then you can use the `-it` flag.  
The `--name` flag assigns a user defined identifier to the container.  
The `-p <Host Port>:<Container Port>` flag maps/exposes the `<Container Port>` to the `<Docker Host Port>`  
The `-e` flag allows you to define any ENVIRONMENT VARIABLE that you would like to define.

You can find out about other helpful [Docker flags](https://docs.docker.com/engine/reference/run/)

### **Inspecting a running Container**

`docker ps` lists all the running containers.  
`docker inspect <container-name>` outputs more in-depth details of that container.  
`docker logs <container-name>` outputs the messages written to std-out and std-err streams.

### **Docker run: Run commands inside Container**
The `docker run <Container-name> <command>` command executes the specified command inside the container. For example: `docker run ubuntu ps`  
The `-it` flag can be added to execute the coammand in an interactive way. `docker run -it ubuntu bash`

### **Building Docker images using Dockerfile**

Till now we ran containers based on pre-built images from DockerHub, now we are going to look at building our own images using Dockerfile.  
Here is a sample Ubuntu Dockerfile.  
```
FROM ubuntu:18.04

RUN apt-get update && apt-get install -y iputils-ping && apt-get -y install nginx 

COPY default /etc/nginx/sites-enabled/default

EXPOSE 80

CMD /etc/init.d/nginx restart && bash
```  
Here we are building upon a base Ubuntu Image. We want to run Nginx in this container, so we update package information and install Nginx. We then copy the necessary config file to the appropriate location inside the container. We espose the port 80 to the Docker host. We then follow it with the necessary coammnds to be excuted once the container is running. 

### **CMD vs Entrypoint vs Run**

`RUN` defines commands that need to be executed in the process of building the image. Whereas `CMD` and `ENTRYPOINT` defines commands that must be executed upon execution/starting of the container.  
The difference between `CMD` and `ENTRYPOINT` can be confusing at times, but I'll try my best to explain. `ENTRYPOINT` defines the default command that should be executed with the starting of the container. For example for Ubuntu images by default it is `/bin/sh -c`. `CMD` defines commands that need to be executed once the container is up and running. Here is a great discussion on the differences between `CMD` and `ENTRYPOINT`.  
[What is the difference between CMD and ENTRYPOINT in a Dockerfile?](https://stackoverflow.com/questions/21553353/what-is-the-difference-between-cmd-and-entrypoint-in-a-dockerfile) 

### **Volumes**

For persisting data in the containers a great way is to map a local directory to a directory in the container using the `-v <Host Directory>:<Container Directory>` flag. This can be marked as read-only adding the `:ro` parameter. `-v <Host Directory>:<Container Directory>:ro`  
For sharing a volume with a new container which is already being used by another container, we simply add `--volumes-from <Container-name>`.  

### **Networks**

A network for the Containers to communicate with each other can be easily created using the `docker network create <network-name>` and can be added in the docker run command using the `--net=<network-name>`. The `docker network connect <container-name>` can be used to connect an existing container to the network.

### **Docker-compose**

It is rarely ever the case that our final application requires just one container to run. So to manage multiple different containers who each play a different role we need a program to manage them. This can be done manually, which will be quite tedious or using a file containing the necessary parameters required to run the containers. This is where Docker-compose comes in. It is a file which contains the necessary details for every container that we need.

Here is an example `docker-compose.yml` file. This file is creating a Nginx Load Balancer, MySQL DB and Wordpress Container. There are 3 `replicas` of the WP container, the traffic to which will be controlled by the Nginx Proxy.

```
version: "3.3"
services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    container_name: nginx1-proxy
    ports:
      - "80:80"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    environment: 
      DEFAULT_HOST: blog.DOMAIN.TLD
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    expose: 
      - "3306"
    env_file: 
      - mysql-variables.env
    container_name: mysql_db

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    expose:
      - "80"
    env_file: 
      - wp-variables.env
    deploy:
      mode: replicated
      replicas: 3

volumes:
    db_data: {}

networks:
  default:
    external:
      name: nginx-proxy
```
The command used to run this `docker-compose up -d --compatiility`

Instead of using Docker hub images we can build our own images using `Docker-file` and `build: .`

The `docker-compose.yml` consists of commands that we were already running but in a different from that requires less of human interaction. 

***I'll be adding more topics to this document as I continue to explore Docker.***

