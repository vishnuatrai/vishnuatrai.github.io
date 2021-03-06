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

<!--more-->

<table class="kubtable">
	<tr style="font-weight: bold;">
		<th style="width: 16%"></th>
		<th style="width: 42%"> <strong>Secret</strong></th>
		<th style="width: 42%"> <strong>ConfigMap</strong></th>
	</tr>
	<tbody>
		<tr>
		<td><strong>What is?</strong></td>
		<td>Secrets are k8s object to manage small amount of sensitive data like password, keys and tokens with less than 1mb size. Secrets encoded and stored inside k8s master etcd data store. Since Secrets will be created outside of pods and containers, these can be used any number of times</td>
		<td>ConfigMaps used to seperate container images and its custom configurations so that images are portable and can be run in any environment providing appropriate configuration. `ConfigMap` stores data in key, value format. If any configuration values are sensitive the use `Secret` instead `ConfigMap`. Its must to create `ConfigMap` before hand if we need to refer in pod spec</td>
		</tr>
		<tr>
		<td><strong class="feature">Create using `kubectl`</strong></td>
		<td >
			<span class="syntax">	
				#syntax <br/>
				kubectl create secret <type of secret (generic)> <name of secret> < data from-file|from-literal><br/>
				echo -n 'admin' > ./username.txt<br/>
				echo -n '1f2d1e2e67df' > ./password.txt<br/>
				kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt<br/>
				kubectl create secret generic dev-db-secret --from-literal=username=user --from-literal=password='S!B\*d$zDsb='
			</span>
		</td>
		<td>
			<span  class="syntax">
				#syntax<br/>
				kubectl create configmap <name of configmap> < data from-file|from-literal><br/>
				path/to/config/file/application.yaml<br/>
				path/to/config/file/application-prod.properties<br/>
				kubectl create configmap app-config --from-file=path/to/config/file/<br/>
				#list the content of properties and yml file configuration<br/>
				kubectl get configmaps app-config -o wide<br/>
				kubectl create configmap custom-config --from-literal port=8080 --from-literal https=false
			</span>
		</td>
		</tr>
		<tr>
			<td><strong class="feature">Create using yaml manifesto files</strong></td>
			<td>
				<span  class="syntax">
				<pre>
					<code>
	#sample yaml manifesto secret.yaml file 
	apiVersion: v1
	kind: Secret
	metadata:
		name: mysecret
	type: Opaque
	data:
		username: appuser
		password: MWYyZDFlMmU2N2Rm
&nbsp;
&nbsp;
create using kubectl apply
	kubectl apply -f ./secret.yaml
				</span>
			</td>
			<td>
			<span  class="syntax">
				<pre>
				<code>
	#sample yaml manifesto configmap.yaml file 
	apiVersion: v1
	kind: ConfigMap
	metadata:
		name: myconfig
	type: Opaque
	data:
		port: 8080
		https: false
&nbsp;
&nbsp;
&nbsp;
create using kubectl apply
	kubectl apply -f ./configmap.yaml
				</code>		
				</pre>		
			</span>
			</td>
		</tr>
		<tr>
			<td>
				<strong class="feature">
					Deploy within Pods, 
					Using Volumes
				</strong>
			</td>
			<td>
				<span  class="syntax">
				<pre>
				<code>
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
					  - name: secret-volume
					    mountPath: /etc/secret
					    readOnly: true
					volumes:
					- name: secret-volume
					  secret:
					  	secretName: mysecret	
				</code>		
				</pre>		
			    </span>
			</td>
			<td>
				<span  class="syntax">
				<pre>
				<code>
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
				</code>
				</pre>
				</span>
			</td>
		</tr>
		<tr>
			<td>
				<strong class="feature">
					Deploy within Pods, 
					Using environment variables
				</strong>
			</td>
			<td>
				<span class="syntax">
					<pre><code>
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


Instead of volume mapping use environment variables
and use secret(ie. mysecret) to assign values. 
Verify the environment variables.
&nbsp;
	kubectl exec nginx env | grep SECRET
		=> SECRET_PASSWORD=MWYyZDFlMmU2N2Rm
		=> SECRET_USERNAME=appuser
					</code></pre>
				</span>
			</td>
			<td>
				<span class="syntax"><pre><code>
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


Instead of volume mapping use environment variables
and use configmap(ie. myconfig) to assign values.
Verify the environment variables.
&nbsp;
	kubectl exec nginx env | grep CONFIG
		=> CONFIG_PORT=8080				
				</code></pre></span>
			</td>
		</tr>
	</tbody>
</table>
