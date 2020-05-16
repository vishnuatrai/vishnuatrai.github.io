---
layout: post
title: "Kubernetes - Secrets vs ConfigMaps"
date: 2018-08-16 16:28:34 +0530
comments: true
description: Kubernetes - Secrets vs ConfigMaps
keywords: Kubernetes, Secrets, ConfigMaps
categories:
  Kubernetes
  Secrets
  ConfigMaps
  DevOps
---

###Secrets
Serets are k8s object to manage small amount of sensitive data like password, keys and tokens with less than 1mb size. Secrets encoded and stored inside k8s master etcd data store. Since Secrets will be created outside of pods and containers, these can be used any number of times.
####1. Create `secret` using `kubectl`<!--more-->
	#syntax
	kubectl create secret <type of secret (generic)> <name of secret> <data from-file|from-literal>
	echo -n 'admin' > ./username.txt
	echo -n '1f2d1e2e67df' > ./password.txt
	kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
	kubectl create secret generic dev-db-secret --from-literal=username=user --from-literal=password='S!B\*d$zDsb='
####2. Create `Secret` using yaml menifesto files
	#sample yaml menifesto secret.yaml file 
	apiVersion: v1
	kind: Secret
	metadata:
		name: mysecret
	type: Opaque
	data:
		username: appuser
		password: MWYyZDFlMmU2N2Rm
create using kubectl apply
	kubectl apply -f ./secret.yaml

####Deploy Secrets in Pods
####1. Through Volumes
	#sample pod manifesto yaml file
	apiVersion: v1
	kind: Pod
	metadata:
		name: nginx
	spec:
		containers:
		- name: nginx
		  image: nginx
		  volumeMounts:
		  - name: secret-volumn
		    mountPath: /etc/secret
		    readOnly: true
		volumes:
		- name: secret-volumn
		  secret:
		  	secretName: mysecret
Add a volume section with type `secret` and with `secretName`(ie. mysecret already created) and then mount the secret volumn to container target location that is similar to mounting any other volumes.
####2. Through environment variables
	#sample pod manifesto yaml file
	apiVersion: v1
	kind: Pod
	metadata:
		name: nginx
	spec:
		containers:
		- name: nginx
		  image: nginx
		  env:
		  - name: SECRET_USERNAME
		    valueFrom:
		    	secretKeyRef:
		    		name: mysecret
		    		key: username
		  - name: SECRET_PASSWORD
		    valueFrom:
		    	secretKeyRef:
		    		name: mysecret
		    		key: password
Instead of volume mapping use environment variables and use secret(ie. mysecret) to assign values. Verify the environment variables.
	kubectl exec nginx env | grep SECRET
		=> SECRET_PASSWORD=MWYyZDFlMmU2N2Rm
		=> SECRET_USERNAME=appuser


###ConfigMaps
ConfigMaps used to seperate container images and its custom configurations so that images are portable and can be run in any environment providing appropriate configuration. `ConfigMap` stores data in key, value format. If any configuration values are sensitive the use `Secret` instead `ConfigMap`. Its must to create `ConfigMap` before hand if we need to refer in pod spec.

####1. Create `configmap` using `kubectl`<!--more-->
	#syntax
	kubectl create configmap <name of configmap> <data from-file|from-literal>
	path/to/config/file/application.yaml
	path/to/config/file/application-prod.properties
	kubectl create configmap app-config --from-file=path/to/config/file/
	kubectl get configmaps app-config -o wide #list the content of properties and yml file configuration
	kubectl create configmap custom-config --from-literal port=8080 --from-literal https=false
####2. Create `ConfigMap` using yaml menifesto files
	#sample yaml menifesto configmap.yaml file 
	apiVersion: v1
	kind: ConfigMap
	metadata:
		name: myconfig
	type: Opaque
	data:
		port: 8080
		https: false
create using kubectl apply
	kubectl apply -f ./configmap.yaml

####Deploy ConfigMaps in Pods
####1. Through Volumes
	#sample pod manifesto yaml file
	apiVersion: v1
	kind: Pod
	metadata:
		name: nginx
	spec:
		containers:
		- name: nginx
		  image: nginx
		  volumeMounts:
		  - mountPath: /target/
		    name: custom-config
		volumes:
		- name: custom-config
		  configMap:
		  	name: app-config
		  	items:
		  	- key: application.yaml  
		  	  path: application.yaml
		  	- key: application-prod.properties
		  	  path: application-prod.properties
In `volumes` section `key` is file name created while configMap, `path` is target location where data will be present inside the pod.
in `volumeMounts` we provide the mountPath the target directory to mount the configMap volume.
####2. Through environment variables
	#sample pod manifesto yaml file
	apiVersion: v1
	kind: Pod
	metadata:
		name: nginx
	spec:
		containers:
		- name: nginx
		  image: nginx
		  env:
		  - name: CONFIG_PORT
		    valueFrom:
		    	configMapKeyRef:
		    		name: myconfig
		    		key: port
Instead of volume mapping use environment variables and use configmap(ie. myconfig) to assign values. Verify the environment variables.
	kubectl exec nginx env | grep CONFIG
		=> CONFIG_PORT=8080



