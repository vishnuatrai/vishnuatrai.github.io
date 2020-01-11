---
layout: post
title: "Jenkins Slave with Docker Executors"
date: 2019-08-08 16:35:25 +0530
comments: true
description: Jenkins Slave with Docker Executors
keywords: Jenkins, Docker Executors, DevOps
categories:
  Docker
  Docker&nbsp;Image
  Dive
  DevOps
---


[Docker Plugin](https://wiki.jenkins.io/display/JENKINS/Docker+Plugin) enables jenkins to run jobs as docker container. In such case we dont need to setup jenkins nodes(agents) with specific binaries, instead docker images will be used to run jobs.<!--more-->Jenkins master needs to be configured with docker host where we can push docker images, those will be used by agents to execute jobs. 


###Global Configuration
After installation of `Docker Plugin` from manage plugins options, we will need to configure docker host and templates to create docker executers in Global Configuration option.

Go to **Global Configurations** -> **Docker** -> fill the below configuration options to setup docker host <br />
**Name**    Name of docker slave to be used in job configuration  <br />
**Docker Url**    docker engine url and port  <br />

![](/images//posts/Jenkins7.png)

**Docker Template**    docker template is an executor, we can add multiple templates and it will enable multiple executors  <br />
Fill below configurations to setup docker template <br />

- **Docker Image** image name that that is available on docker host and having binaries to run the job
- **Container Settings** > **Volumes** Provide the container volumes(ie. `/home/dockerslave/bin`) where the binaries available, these are mapped with host machine and binaries will be available on host machine to run the job.
- **Label** Provide the label to uniquely identify the executor and configure in job configuration
- **Launch Method** select **Docker ssh compute launcher**
- **Credentials** select credential id to ssh into docker host container

![](/images//posts/Jenkins4.png)
![](/images//posts/Jenkins5.png)
![](/images//posts/Jenkins6.png)
###Job Configuration
**Build** > check **Restrict where this project can be run** > **Label Expression** > provide the docker template lable

