---
layout: post
title: "Kafka - Consumer CommitFailedException for revoked partition"
date: 2019-06-10 16:30:34 +0530
comments: true
description: Kafka - Consumer CommitFailedException for revoked partition
keywords: Kafka, Consumer, CommitFailedException, Revoked Partition
categories:
  Kafka
  Consumer
  CommitFailedException
  Revoked&nbsp;Partition
---

####Error Trace
	06-10 01:15:05 131 pool-9-thread-5 ERROR [] -
	commit failed
	org.apache.kafka.clients.consumer.CommitFailedException: Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. This means that the time between subsequent calls to poll() was longer than the configured max.poll.interval.ms, which typically implies that the poll loop is spending too much time message processing. You can address this either by increasing the session timeout or by reducing the maximum size of batches returned in poll() with max.poll.records.
	at org.apache.kafka.clients.consumer.internals.ConsumerCoordinator.sendOffsetCommitRequest(ConsumerCoordinator.java:713) ~[MsgAgent-jar-with-dependencies.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerCoordinator.commitOffsetsSync(ConsumerCoordinator.java:596) ~[MsgAgent-jar-with-dependencies.jar:na]
	at org.apache.kafka.clients.consumer.KafkaConsumer.commitSync(KafkaConsumer.java:1218) ~[ConsumerService-jar-with-dependencies.jar:na]
	at com.vatrai.consumer.service.ConsumerServiceImpl.consume(ConsumerServiceImpl.java:67) ~[ConsumerService-jar-with-dependencies.jar:na]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [na:1.8.0_161]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [na:1.8.0_161]
	at java.lang.Thread.run(Thread.java:748) [na:1.8.0_161]

####Reason
Consumer invoked the polling method to obtain the messages.<!--more--> Client application uses an external loop to continuously invoke the polling method of the consumer. The next poll was performed only after all messages in a batch were processed. `max.poll.interval.ms` option can be configured to set maximum interval between two polling operations. If client application cannot process polling records and it takes more time than the maximum interval. As a result, the server may remove the client and trigger rebalance.

####Optimize Configuration
	
***max.poll.interval.ms=75000***

The maximum delay between invocations of poll() when using consumer group management. This places an upper bound on the amount of time that the consumer can be idle before fetching more records. If poll() is not called before expiration of this timeout, then the consumer is considered failed and the group will rebalance in order to reassign the partitions to another member. For consumers using a non-null group.instance.id which reach this timeout, partitions will not be immediately reassigned. Instead, the consumer will stop sending heartbeats and partitions will be reassigned after expiration of session.timeout.ms. This mirrors the behavior of a static consumer which has shutdown.


***max.poll.records = 300***

The maximum number of records returned in a single call to poll(). Number of polling records can be reduced if processing these records taking more time.



