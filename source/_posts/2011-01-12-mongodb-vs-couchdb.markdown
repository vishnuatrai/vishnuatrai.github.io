---
author: admin
comments: true
date: 2011-01-12 17:14:11+00:00
layout: post
slug: mongodb-vs-couchdb
title: MongoDB V/S CouchDB
wordpress_id: 139
categories:
- Technology
tag:
- CouchDB
- MongoDb
- NoSQL
---

Both MongoDB and CouchDB are document oriented databases with JSON style object data storage.  They have their pros and cons in different situations.


#### 1) Map Reduce


Mongo uses map reduce for data processing jobs, Couch uses map reduce for build all views.


#### 2) Atomicity 


Both support concurrent modifications of single documents.  Both forego complex transactions involving large numbers of objects.


#### 3) Interface 


Couch uses REST as its interface to the database. Mongo relies on language specific database drivers. 


#### 4) Query Expression 


Couch uses index building schemes to generate indexes those support particular queries. Mongo uses traditional dynamic queries and query optimizer to determine whether index exists or not. 


#### 5) Horizontal Scalability


Couch uses replication as a way to scale, instead Mongo uses sharding as a way to scale that is similar to Google's BigTable.


#### 6) Storage strategy


Couch MVCC based, Mongo uses traditional approach and updates an object in place when possible.




### Use cases


Couch is very good for certain classes of problems: 
   problems which need intense versioning; problems with offline databases that resync later;
   problems where you want a large amount of master-master replication happening.

Mongo is very good for different kind of problems: 
    problems requiring high update rates of objects are a great fit; compaction is not necessary.
Mongo is more oriented towards master/slave and auto failover configurations than to complex master-master setups.     
With MongoDB you should see high write performance, especially for updates.     
