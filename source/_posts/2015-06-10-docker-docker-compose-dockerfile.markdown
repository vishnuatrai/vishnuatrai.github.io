---
layout: post
title: "Docker, Docker compose and Dockerfile"
date: 2015-06-10 18:29:15 +0530
comments: true
description: Docker, Docker compose and Dockerfile, Docker hub
keywords: Docker, Docker compose, Dockerfile, DevOps
categories:
  Docker
  Docker&nbsp;compose
  Dockerfile
  Docker&nbsp;hub
  DevOps
---

###What is Docker

The Docker uses the Linux kernel and features of the kernel, like Cgroups and namespaces, to segregate processes so they can run independently. This independence is the intention of containers‐the ability to run multiple processes and apps separately from one another to make better use of your infrastructure while retaining the security you would have with separate systems.

Containers are the organizational units of Docker. When we build an image and start running it; we are running in a container.<!--more-->

Unlike the VMs which can communicate with the hardware of the host (ex: Ethernet adapter to create more virtual adapters) Docker containers run in an isolated environment on top of the host's OS.

###What is Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration.

Docker Compose lets you bring up a complete development environment with only one command: docker-compose up, and tear it down just as easily using docker-compose down. This allows us developers to keep our development environment in one central place and helps us to easily deploy our applications.

Compose works in all environments: production, staging, development, testing, as well as CI workflows. You can learn more about each case in Common Use Cases.

Using Compose is basically a three-step process:

    Define your app’s environment with a Dockerfile so it can be reproduced anywhere.

    Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

    Run docker-compose up and Compose starts and runs your entire app.

A docker-compose.yml looks like this:

	version: '3'
	services:
	  web:
	    build: .
	    ports:
	    - "5000:5000"
	    volumes:
	    - .:/code
	    - logvolume01:/var/log
	    links:
	    - redis
	  redis:
	    image: redis
	volumes:
	  logvolume01: {}

Docker Compose commands - 


	$ docker-compose up -d
	$ docker-compose down
	$ docker-compose start
	$ docker-compose stop
	$ docker-compose build
	$ docker-compose logs -f db
	$ docker-compose scale db=4
	$ docker-compose events
	$ docker-compose exec db bash

###What is Dockerfile

Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

The docker build command builds an image from a Dockerfile and a context. The build’s context is the set of files at a specified location PATH or URL. The PATH is a directory on your local filesystem. The URL is a Git repository location.

A context is processed recursively. So, a PATH includes any subdirectories and the URL includes the repository and its submodules. This example shows a build command that uses the current directory as context:

	$ docker build .

The build is run by the Docker daemon, not by the CLI. The first thing a build process does is send the entire context (recursively) to the daemon. In most cases, it’s best to start with an empty directory as context and keep your Dockerfile in that directory. Add only the files needed for building the Dockerfile.

To use a file in the build context, the Dockerfile refers to the file specified in an instruction, for example, a COPY instruction. To increase the build’s performance, exclude files and directories by adding a .dockerignore file to the context directory.

You use the -f flag with docker build to point to a Dockerfile anywhere in your file system.

	$ docker build -f /path/to/a/Dockerfile .


***Dockerfile Instructions and Commands***


`FROM`, It defines the base image to use to start the build process. 

`LABEL`, add labels to your image to help organize images by project, record licensing information

`RUN`, takes a command as its argument and runs it to form the image. it actually is used to build the image

`EXPOSE`, used to indicate the ports on which a container listens for connections, Its used to associate a specified port to enable networking between the running process inside the container and the outside world.

`ENV`, used to set the environment variables (one or more). These variables consist of “key value” pairs.

`ADD` or `COPY`, these commands gets two arguments: a source and a destination. It basically copies the files from the source on the host into the container’s own filesystem at the set destination. Although ADD and COPY are functionally similar, COPY is preferred. That’s because it’s more transparent than ADD. 

`CMD`, can be used for executing a specific command. it's executed when a container is instantiated using the image being built. It should be considered as an initial, default command that gets executed with the creation of containers based on the image.

	# Usage 1: CMD application "argument", "argument", ..
	FROM ubuntu
	CMD echo "This is a test."

`ENTRYPOINT`, argument sets the concrete default application that is used every time a container is created using the image. For example, if you have installed a specific application inside an image and you will use this image to only run that application, you can state it with ENTRYPOINT and whenever a container is created from that image, your application will be the target.

If you couple ENTRYPOINT with CMD, you can remove “application” from CMD and just leave “arguments” which will be passed to the ENTRYPOINT.

	# Usage: ENTRYPOINT application "argument", "argument", ..
	# Remember: arguments are optional. They can be provided by CMD
	#           or during the creation of a container.
	ENTRYPOINT echo

	# Usage example with CMD:
	# Arguments set with CMD can be overridden during *run*
	CMD "This is a test."
	ENTRYPOINT echo  


The best use for ENTRYPOINT is to set the image’s main command, allowing that image to be run as though it was that command (and then use CMD as the default flags).

Let’s start with an example of an image for the command line tool s3cmd:

	ENTRYPOINT ["s3cmd"]
	CMD ["--help"]

The ENTRYPOINT instruction can also be used in combination with a helper script, allowing it to function in a similar way to the command above, even when starting the tool may require more than one step.

For example, the Postgres Official Image uses the following script as its ENTRYPOINT:

	#!/bin/bash
	set -e

	if [ "$1" = 'postgres' ]; then
	    chown -R postgres "$PGDATA"

	    if [ -z "$(ls -A "$PGDATA")" ]; then
	        gosu postgres initdb
	    fi

	    exec gosu postgres "$@"
	fi
	
	exec "$@"

The helper script is copied into the container and run via ENTRYPOINT on container start:

	COPY ./docker-entrypoint.sh /
	ENTRYPOINT ["/docker-entrypoint.sh"]
	CMD ["postgres"]

This script allows the user to interact with Postgres in several ways.

It can simply start Postgres:

	$ docker run postgres

Or, it can be used to run Postgres and pass parameters to the server:

	$ docker run postgres postgres --help

Lastly, it could also be used to start a totally different tool, such as Bash:

	$ docker run --rm -it postgres bash

`VOLUME`, used to expose any storage area, configuration storage, or files/folders created by docker container

`USER`, used to set the UID (or username) which is to run the container based on the image being built.
If a service can run without privileges, use USER to change to a non-root user. Start by creating the user and group in the Dockerfile with something like RUN groupadd -r postgres && useradd --no-log-init -r -g postgres postgres.

	# Usage: USER [UID]
	USER 800

To reduce layers and complexity, avoid switching USER back and forth frequently.

`WORKDIR`, used to set where the command defined with CMD is to be executed

###What is Docker Hub

Docker Hub is a cloud-hosted version of Docker Registry. A Docker user can opt for Docker Registry, which is a stateless, open source and scalable server-side application, if they prefer to maintain the storage and distribution of Docker images instead of relying on Docker's service.


##References
https://docs.docker.com/