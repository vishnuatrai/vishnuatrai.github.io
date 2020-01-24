---
layout: post
title: "MongoDB Change Streams - Real time oplog notifications"
date: 2018-07-05 17:19:31 +0530
comments: true
description: MongoDB Change Streams - Real time oplog notifications
keywords: MongoDB, ChangeStreams
categories:
  MongoDB
  Change&nbsp;Streams
  ChangeStreams
  DevOps
---

Change Stream is easiest way to subscribe database changes realtime, its based on mongoDB oplog(Operation Log) technology. An ideal use case would be ETL operational data to a reporting and visualization data store via kafka data pipeline.

###MongoDB real time data sync technologies
1. mongodb oplog technology based changestream enable applications to stream real-time data changes<!--more-->
2. changestream can notify your application of all writes to documents (including deletes) without polling.
3. changestream advantages over oplog


##Use case
MongoDB changestream (operation logs, create, update, delete) will be as data source. Java springboot based application to subscribe changes and push to kafka topic. Kafka data pipeline can refine data, transform, filter and sync in to a big data storage for reporting, visualization or many other purpose.

	###Java sample
	import com.mongodb.Block;
	import com.mongodb.MongoClient;
	import com.mongodb.MongoClientURI;
	import com.mongodb.client.MongoCollection;
	import com.mongodb.client.MongoDatabase;
	import com.mongodb.client.model.changestream.FullDocument;
	import com.mongodb.client.model.changestream.ChangeStreamDocument;
	import org.bson.Document;


	MongoClient mongoClient = new MongoClient(new MongoClientURI("mongodb://localhost:27017,localhost:27018,localhost:27019"));
	MongoDatabase database = mongoClient.getDatabase("test");
	MongoCollection<Document> collection = database.getCollection("restaurants");

	Block<ChangeStreamDocument<Document>> printBlock = new Block<>() {
    @Override
    	public void apply(final ChangeStreamDocument<Document> changeStreamDocument) {
        	System.out.println(changeStreamDocument);
    	}
	};

	collection.watch().forEach(printBlock);


###changestream advantages over oplog
1. ***Use access control*** (no admin user required to access changestream)
2. ***Present a Defined API***, API syntax takes advantage of the established MongoDB query language, and are independent of the underlying oplog format.
3. ***Total Ordering***, subscriber applications will always receive changes in the order they were applied to the database.
4. ***Filters***, Changes can be filtered to provide relevant and targeted changes to listening applications. As an example, filters can be on operation type or fields within the document.
5. ***Power of aggregation***, define change streams on any collection just like any other normal aggregation operators using `$changeStream` operator and `watch()` method
