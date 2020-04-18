---
layout: post
title: "ProxySQL - MySQL High Availability Load Balancing"
date: 2018-11-12 22:59:40 +0530
comments: true
description: ProxySQL - MySQL High Availability Load Balancing
keywords: ProxySQL, DevOps, MySQL HAProxy
categories:
  ProxySQL
  MySQL&nbsp;HAProxy
  MySQL
  DevOps
---


###What is ProxySQL?
- Lightweight Proxy layer for MySQL
- Its protocol aware that we put between application and database server
- Improve database operations <!--more-->
- Understand and solve performance issues
- A proxy layer to shield the database
- Add high availability to database topology

###Why ProxySQL/HAProxy?
ProxySQL provides below advantages if included in deployment stack 

- Scalability <br />
	Connection Pooling and multiplexing,  Read/Write split,  Read/Write sharding
- High Availability <br />
	Seamless failover,  Load balancing,  Cluster aware
- Advance query and support <br />
	Query caching,  Query rewrite,  Query blocking,  Query mirroring,  Query throttling,  Query timeout
- Manageability <br />
	Admin Utility,  Runtime reconfiguration,  Monitoring

###ProxySQL Configurations Options
	mysql_replication_hostgroups
	mysql_servers
	mysql_server_connect_log
	mysql_server_ping_log
	mysql_server_replication_lag_log
	mysql_server_read_only_log
	stats_mysql_connection_pool
	stats_mysql_commands_counters
	mysql_query_rules
	stats_mysql_query_digest

###Deploying with Kubernetes/Openshift - The Sidecar Pattern
We can deploy the `proxysql` image in same pod as application image but data base references we will need to provide as `localhost` or `127.0.0.1` and port will be `6033` (the default proxysql port). The application will connect to proxysql server and it will then redirect database queries to mysql database cluster.

Openshift/kubernetes template spec -

	spec:
	 template:
	  spce:
	   volumes:
	    -
	   name: "proxysql-configiguration-file"
	     secret:
	      secretName: "proxysql-configuration"
	   containers:
	    -
	   name: "proxysql-service"
	     image: "docker.com/proxysql/proxysql:1.0"
	     ports:
	      -
	   containerPort: 6033
	       protocal: TCP
	   env:
	   resources:
	   volumeMounts:
	    -
	   name: "proxysql-configuration"
	     readOnly: true
	     mountPath: "/etc/prosysql-config"

###References

https://www.proxysql.com/


docker sandbox



https://github.com/vishnuatrai/MySQLSandbox


github



https://github.com/sysown/proxysql




