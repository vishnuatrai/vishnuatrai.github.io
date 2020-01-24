---
layout: post
title: "MongoDB Replica Set - localhost and Ops Manager"
date: 2017-04-21 17:28:18 +0530
comments: true
description: MongoDB Replica Set - localhost and Ops Manager
keywords: MongoDB, Replica Set, ReplicaSet, DevOps
categories:
  MongoDB
  Replica&nbsp;Set
  ReplicaSet
  DevOps
---

###Setup Local DevEnv Replica Set

1. update `mongod.conf` for replication 


		#edit /usr/local/etc/mongod.conf
		systemLog:
	  		destination: file
	  		path: /usr/local/var/log/mongodb/mongo.log
	  		logAppend: true
		storage:
	  		dbPath: /usr/local/var/mongodb
		net:
	  		bindIp: 127.0.0.1
		replication:
	  		replSetName: rs0
	  		oplogSizeMB: 100

<!--more-->
2. Restart mongodb service 
		
		brew services restart mongodb

	<br/>
3. log on to `mongo` shell and initiate replica set

		rs.initiate({_id: "rs0", members: [{_id: 0, host: "127.0.0.1:27017"}] })

   verify replication members with `rs.status()`


### Mongodb Ops Manager

Ops Manager is a management application from mongodb and its available as part of MongoDB Enterprise. Ops Manager enables to configure and maintain MongoDB nodes and clusters.

***Server Pool***

Ops Manager Server Pool allows Ops Manager users with administrative privileges, i.e. Ops Manager Administrators, to maintain a pool of provisioned servers that already have installed. When users in a project want to create a new MongoDB deployment, they can request servers from this pool to host the MongoDB deployment.

If you manage large or multiple MongoDB clusters, you probably find yourself scaling them up, and perhaps down, on demand. And if you’re providing MongoDB as a service to your developer teams, scaling requirements might come with little to no notice.

With a few clicks in the Ops Manager GUI, or via a simple API call, you can add new nodes to a cluster, and remove then when they are no longer needed. MongoDB automatically rebalances data as your topology changes, all without service impact.
