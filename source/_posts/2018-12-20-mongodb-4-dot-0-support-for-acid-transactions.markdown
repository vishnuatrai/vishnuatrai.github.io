---
layout: post
title: "MongoDB 4.0 - Support for ACID transactions"
date: 2018-12-20 17:41:34 +0530
comments: true
description: MongoDB 4.0 - Support for ACID transactions
keywords: MongoDB, MongoDB ACID, DevOps
categories:
  MongoDB
  MongoDB&nbsp;4.0
  MongoDB&nbsp;ACID
  DevOps
---

###Support for Single-Shard multi document ACID transactions
To support benefits of ACID transactions with fully-denormalized document data modeling, MongoDB added single-shard ACID transaction support in the released 4.0. These single-shard transactions apply ACID properties on updates across multiple documents those are present in the same shard. Multi-shard transactions supposed to implement in release 4.2.

####Scenario, how it works?
1. Client application will get a database session <!--more-->
2. Database session initiate transaction block using `start_transaction` statement with `majority` `writeConcern` in `try` block. The only `writeConcern` thats suitable for high data durability is that of majority. This means a majority of replicas should commit the changes before the primary acknowledges the success of the write to the client. The transaction will remain blocked till at least 1 of the 2 secondaries pulls the update from the primary using asynchronous replication which is susceptible to unpredictable replication lag especially under heavy load. 

3. Within transaction block multiple insert/update/remove statements can be made.
4. At end of transaction block `commit_transaction` required to commit data in database.
5. If any exception occurs in transaction block, client application should `abort_transaction` in `catch` block to roll back updates.
6. Finally the database client session can be closed in `finally` block

Sample Java code to mimic same scenario, 

	###Java sample
	private void multiDocumentTransactions() {
        ClientSession session = client.startSession();
        try {
            session.startTransaction(TransactionOptions.builder().writeConcern(WriteConcern.MAJORITY).build());
            orders.updateOne(session, {..filter..}, {..updates..});
            stock.updateOne(session, {..filter..}, {..updates..});
            session.commitTransaction();
        } catch (MongoCommandException e) {
            session.abortTransaction();
            //ROLLBACK TRANSACTION
        } finally {
            session.close();
        }
    }
