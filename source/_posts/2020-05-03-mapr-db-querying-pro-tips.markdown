---
layout: post
title: "MapR-DB - Querying Pro-tips"
date: 2020-05-03 13:09:15 +0530
comments: true
description: MapR-DB - Querying Pro-tips
keywords: MaprDB, MapR-DB, Querying, Pro-tips
categories:
  MaprDB
  MapR-DB
  Querying
  Pro-tips
  Hadoop
  Commercial-Hadoop
---
###What is MapR?
MapR is a complete enterprise-grade distribution for Apache Hadoop. The MapR Data Platform has been developed to improve Hadoop’s reliability, performance, and ease of use. The MapR provides a full Hadoop stack that includes the MapR File System (MapR-FS), the MapR-DB NoSQL database management system, MapR Streams, the MapR Control System (MCS) user interface, and a full family of Hadoop ecosystem projects.<!--more-->

###Querying
MapR provides native JSON (JavaScript Object Notation) support to MapR Database. The JSON access layer is known as OJAI(Open JSON Application Interface).
####Querying dependency
The workstation must setup MapR client and its certificates and should be able to login to `mapr dbshell`
####Java program setup
The workstation must setup MapR client and its certificates
	pom.xml
	<dependency>
		<groupId>org.ojai</groupId>
		<artifactId>ojai</artifactId>
	</dependency>
	<dependency>
		<groupId>com.mapr.db</groupId>
		<artifactId>maprdb</artifactId>
	</dependency>

####1. Find by id
	mapr dbshell> find /datalake/path/to/table/users --c {"$eq":{"_id":"5e1dcaaea97bd2004c3caccf"}}
	or 
	mapr dbshell> find /datalake/path/to/table/users --q {"$where":{"$and":[{"$eq":{"_id":"5e1dcaaea97bd2004c3caccf"}}]}}
	#Sample Java snippets
	....
	com.mapr.db.Admin admin = MapRDB.newAdmin();
	com.mapr.db.Table maprTable = MapRDB.getTable("/datalake/path/to/table/users");
	org.ojai.Document doc = maprTable.findById("5e1dcaaea97bd2004c3caccf", fieldPaths={"_id","username","fullname","age","dob"});
	....

####2. Query with `$and` and `select` fields
	mapr dbshell> find /datalake/path/to/table/users --q {"$select":["_id","username","fullname","age","dob"],"$where":{"$and":[{"$eq":{"_id":"5e1dcaaea97bd2004c3caccf"}}]}}
	#Sample Java snippets
	....
	import org.ojai.store.QueryCondition.Op;
	org.ojai.store.QueryCondition query = MapRDB.newCondition().and().is("_id", Op.EQUAL, "5e1dcaaea97bd2004c3caccf").close().build();
	org.ojai.DocumentStream doc = maprTable.find(query, fieldPaths={"_id","username","fullname","age","dob"});
	....
####3. Query with conditions and `orderBy`
	mapr dbshell> find /datalake/path/to/table/users --q {"$where":{"$and":[{"$ge":{"dob":{"$date":"2000-01-15"}}}]},"$orderby":{"username":"desc"}}
	#Sample Java snippets
	org.ojai.types.OTimestamp ojaiDOB = new org.ojai.types.OTimestamp("2000-01-15");
	org.ojai.store.QueryCondition condition = MapRDB.newCondition().and().is("dob", Op.GREATER_OR_EQUAL, ojaiDOB).close().build();
	org.ojai.store.Query query = MapRDB.newQuery().where(condition).orderBy("username","DESC")
	org.ojai.DocumentStream docs = maprTable.findQuery(query, fieldPaths={"_id","username","fullname","age","dob"});
####4. Query with conditions and `limit` results
	mapr dbshell> find /datalake/path/to/table/users --q {"$where":{"$and":[{"$ge":{"dob":{"$date":"2000-01-15"}}}]},"$limit":10}
	#Sample Java snippets
	org.ojai.types.OTimestamp ojaiDOB = new org.ojai.types.OTimestamp("2000-01-15");
	org.ojai.store.QueryCondition condition = MapRDB.newCondition().and().is("dob", Op.GREATER_OR_EQUAL, ojaiDOB).close().build();
	org.ojai.store.Query query = MapRDB.newQuery().where(condition).limit(10)
	org.ojai.DocumentStream docs = maprTable.findQuery(query, fieldPaths={"_id","username","fullname","age","dob"});
####5. Query with range conditions with `$ge`,`$gt`,`$le`,`$lt`
	mapr dbshell> find /datalake/path/to/table/users --q {"$where":{"$and":[{"$ge":{"dob":{"$date":"2000-01-15"}}},{"$le":{"dob":{"$date":"2005-01-15"}}}]},"$orderby":{"username":"desc"},"$limit":10}
	#Sample Java snippets
	org.ojai.types.OTimestamp ojaiFromDOB = new org.ojai.types.OTimestamp("2000-01-15");
	org.ojai.types.OTimestamp ojaiToDOB = new org.ojai.types.OTimestamp("2005-01-15");
	org.ojai.store.QueryCondition condition = MapRDB.newCondition().and().is("dob", Op.GREATER_OR_EQUAL, ojaiFromDOB).is("dob", Op.LESS_OR_EQUAL, ojaiToDOB).close().build();
	org.ojai.store.Query query = MapRDB.newQuery().where(condition).orderBy("username","DESC").limit(10)
	org.ojai.DocumentStream docs = maprTable.findQuery(query, fieldPaths={"_id","username","fullname","age","dob"});	
####6. Query with `$or`
	mapr dbshell> find /datalake/path/to/table/users --q {"$where":{"$or":[{"$le":{"dob":{"$date":"2000-01-15"}}},{"$ge":{"age":{"$number":"20"}}}]},"$orderby":{"username":"desc"},"$limit":10}
	#Sample Java snippets
	org.ojai.types.OTimestamp ojaiFromDOB = new org.ojai.types.OTimestamp("2000-01-15");
	org.ojai.store.QueryCondition condition = MapRDB.newCondition().or().is("dob", Op.GREATER_OR_EQUAL, ojaiFromDOB).is("age", Op.LESS_OR_EQUAL, 20).close().build();
	org.ojai.store.Query query = MapRDB.newQuery().where(condition).orderBy("username","DESC").limit(10)
	org.ojai.DocumentStream docs = maprTable.findQuery(query, fieldPaths={"_id","username","fullname","age","dob"});	







