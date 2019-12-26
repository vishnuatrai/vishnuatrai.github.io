---
layout: post
title: "Dive into Docker Image Layers"
date: 2019-12-26 20:48:58 +0530
comments: true
description: Tool to explore docker image layers
keywords: Docker, Docker Image, Dive, DevOps
categories:
  Docker
  Docker&nbsp;Image
  Dive
  DevOps
---

I recently used a tool named [`dive`](https://github.com/wagoodman/dive) to explore docker images developed and maintained by teammates or opensourced docker images. I used to look into `Dockerfile` steps to explore image layes. `dive` brings cli-ui and using arrow and tab keys we can explore each layer in left pane while layer contents listed in right pane.<!--more-->


Essentially `dive` based on docker api and its cache contents and provides commands to explore layer contents and minimize the size of images.

Installation

`brew install dive`

command to explore a image layers

`dive <docker-image-id>`

to build a image using `dive` command

`dive build -t <image-tag> .`
