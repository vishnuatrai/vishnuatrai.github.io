---
layout: post
title: "Rails Applicatoin - Openshift 3.0 deployment"
date: 2016-12-05 18:34:50 +0530
comments: true
description: Rails Applicatoin - Openshift 3.0 deployment
keywords: Rails, RoR, Ruby on Rails, Openshift, Openshift3.0
categories:
  Ruby&nbsp;on&nbsp;Rails
  Rails
  RoR
  Openshift
  Openshift&nbsp;3
---

###Prepare helper scripts for application setup, app start and httpd config

Helper scripts can be stored as part of application code in directory `scripts` in project root directory.

Sample application setup script

	# scripts/application_setup.sh

	#! /bin/bash
	bundle exec rake db:migrate
	bundle exec rake db:seed
<!--more-->	

Sample applicaiton startup script
	
	# scripts/appStartup.sh

	#!/bin/bash
	bundle exec rake assets:precompile

	config-httpd
	exec httpd -D FOREGROUND

Sample script to configure httpd.conf

	# scripts/config_httpd.sh

	#! /bin/sh

	sed -i "s/RailsEnv RAILS_ENV/RailsEnv ${RAILS_ENV}/" /etc/httpd/conf/httpd.conf
	echo "PassengerMinInstances 12" >> /etc/httpd/conf/httpd.conf
	echo "PassengerMaxPoolSize 12" >> /etc/httpd/conf/httpd.conf
	ruby -S passenger-config build-native-support

`httpd.conf` for passenger apache config will include VirtualHost and passenger configs ie. PassengerRoot, PassengerRuby etc.

###Docker Image build with Rails application setup

Sample Dockerfile to build image with rails app. Dockerfile will reside in project root directory.

	FROM centos/passenger-40-centos7
	RUN mkdir -p /opt/app-root/bundle
	COPY ./Gemfile /opt/app-root/bundle/Gemfile
	COPY ./Gemfile.lock /opt/app-root/bundle/Gemfile.lock

	# install sqlite3 on same app image as db, this should be run as seperate docker container service
	RUN sudo apt-get install -y sqlite3 libsqlite3-dev

	WORKDIR /opt/app-root/bundle
	RUN scl enable rh-ruby25 'bundle install --deployment --without capistrano:development:test:int_test --jobs=4'
	RUN chmod -R g+w /opt/app-root/bundle/

	# Copy application code
	COPY . /opt/app-root/src

	WORKDIR /opt/app-root/src
	# Ensure log directory writable, move in the bundle, 
	# set app's default group as root, and put the app startup script in place
	RUN chmod -R g+w /opt/app-root/src/log /opt/app-root/src/db /opt/app-root/src/config /opt/app-root/src/public
	RUN mkdir -p /opt/app-root/src/tmp
	RUN chmod 777 /opt/app-root/src/tmp /opt/app-root/src/log /opt/app-root/src/public
	RUN ln -s /opt/app-root/bundle/.bundle /opt/app-root/src/.bundle
	RUN mkdir -p /opt/app-root/src/vendor
	RUN ln -s /opt/app-root/bundle/vendor/bundle /opt/app-root/src/vendor/bundle

	COPY scripts/application_setup.sh /usr/bin/application_setup.sh
	COPY scripts/appStartup.sh /usr/bin/appStartup.sh
	COPY scripts/config_httpd.sh /usr/bin/config_httpd.sh

	COPY scripts/httpd.conf /etc/httpd/conf/httpd.conf

	CMD ["/usr/bin/appStartup.sh"]

###Build Docker image using Dockerfile and push the image to docker image registry


### Openshift Deployment config 

Using this deployment template we can build a deployment on openshift, these template files can be part of source code in template direcotry of project root dir.

		 oc process -f templates/deployment_config.yml | oc create -f -

Sample 	`DeploymentConfig` template

	  # templates/deployment_config.yml

	  apiVersion: v1
	  kind: DeploymentConfig
	  metadata:
	    name: sample-rails-app
	    labels:
	      app: sample-rails-app
	  spec:
	    strategy:
	      type: Recreate
	      recreateParams:
	        timeoutSeconds: 600
	        mid:
	          failurePolicy: Abort
	          execNewPod:
	            command:
	            - application_setup.sh
	            containerName: sample-rails-app
	    triggers:
	    - type: ConfigChange
	    - type: ImageChange
	    replicas: 1
	    selector:
	      app: sample-rails-app
	      deploymentconfig: sample-rails-app
	    template:
	      metadata:
	        name: sample-rails-app
	        labels:
	          app: sample-rails-app
	          deploymentconfig: sample-rails-app
	      spec:
	        containers:
	        - name: sample-rails-app
	          image: 'docker.hub.com/railsapp/sample-rails-app:0.1'
	          ports:
	          - containerPort: 8080
	          env:
	          - name: RAILS_ENV
	            value: dev
	        restartPolicy: Always

Using service template a service can be created that will be mapped to deployment pods.

		oc process -f templates/service_config.yml | oc create -f -

sample `Service` template

	  # templates/service_config.yml	
	  apiVersion: v1
	  kind: Service
	  metadata:
	    name: sample-rails-app
	    labels:
	      app: sample-rails-app
	  spec:
	    ports:
	    - name: web
	      port: 8080
	      targetPort: 8080
	    selector:
	      app: sample-rails-app
	      deploymentconfig: sample-rails-app

Using routes services will be exposed to external clients, service will be mapped to a route and route will be available to external world. 

	oc process -f templates/route_config.yml | oc create -f -

sample `Route` template

	  # templates/route_config.yml
	  apiVersion: v1
	  kind: Route
	  apiVersion: v1
	  metadata:
	    name: sample-rails-app
	    labels:
	      app: sample-rails-app
	  spec:
	    host: sample-rails-app.openshift-cluster.com
	    to:
	      kind: Service
	      name: sample-rails-app

