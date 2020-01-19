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

###MongoDB real time data sync technologies

Change Stream is easiest way to subscribe database changes realtime, its based on mongoDB oplog(Operation Log) technology. A ideal use case will be ETL operational database to a reporting and visualization data store via kafka data pipeline. 


1. oplog technology based mongodb-elasticsearch real-time data sync
2. changestream technology(MongoDB 3.6+) based mongodb-kafka real-time data sync
3. changestream advantages over oplog





MongoDB oplog (operation logs, create, update, delete) as data source 
Python based open source library(open source adoption) is used 
to run as a OSE service that reads oplog and post changes to elastic search instance
Layer7 secure API gateway to connect elastic search from any infrastructure zone
elasticsearch indexing server provides full text and strict search queries over http
xPack security used to secure elastic search instance while querying or monitoring 
from kibana UI
Elastic search engine is available for query indexes from outside clients


###changestream advantages over oplog

1. Use access control
2. Present a Defined API
3. Scale across nodes
4. Total Ordering
5. Power of aggregation


####mongodb-kafka real-time data sync

MongoDB changestream (data stream of changes, create, update, delete) as data source 
Java springboot based library is used to run as a OSE service that reads MongoDB
changestream and post changes to kafka topics, this library can be a good candidate 
to open source with in optum or public as we are building this for MongoDB edge version
and any java client is not available 
zookeeper used for service registry and discovery of kafka servers
kafka stream processing server monitored by new relic infrastructure management
java based kafka consumer to read messages from kafka topic and publish on MapR DB
MapR DB high performance NoSQL database management system to store reporting data
MapR DB data will be available for external apps for reporting




