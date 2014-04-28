---
author: admin
comments: true
date: 2011-02-16 17:24:29+00:00
layout: post
slug: mapreduce-tool-to-reduce-analytical-data-processing
title: 'MapReduce: Reduce Analytical Data Processing'
wordpress_id: 142
categories:
- Big Data
- MapReduce
---

Today every human being is rapidly increasing online activities and as a result web applications are having a huge amounts of data, now it is a challenge for organizations to quickly analyze the huge amounts of data their customers and audiences generate to better understand and serve them.<!--more--> MapReduce is the tool that is helping those organizations.
  

MapReduce is a programming framework popularized by Google and used to simplify data processing across massive data sets.
  

Enterprise implementation of MapReduce can be found in Hadoop, which uses HDFS to store unstructured data (file system) and uses the power of MapReduce to parallelize the processing of this data. Hadoop is suited for non time-sensitive batch jobs involving large-scale datasets.

With MapReduce, computational processing can occur on data stored either in a filesystem (unstructured) or within a database (structured).

There are two fundamental pieces of a MapReduce query:


#### Map


The master node takes the input, chops it up into smaller sub-problems, and distributes those to worker nodes. A worker node may do this again in turn, leading to a multi-level tree structure. The worker node processes that smaller problem, and passes the answer back to its master node.


#### Reduce


The master node then takes the answers to all the sub-problems and combines them in a way to get the output - the answer to the problem it was originally trying to solve. 



### Benefits of MapReduce


First, the model is easy to use, even for programmers without experience with distributed systems, since it hides the details of parallelization, fault-tolerance, locality optimization, and load balancing. MapReduce allows developers to write applications in their language of choice while handling the details of parallelization behind the scenes.

Second, a large variety of problems are easily expressible as MapReduce computations.
For example, MapReduce is used for the generation of data for Google's production web search service, for sorting, for data mining, for machine learning, and many other systems.

Third, MapReduce enables scaling of applications across large clusters of machines comprising thousands of nodes, with fault-tolerance built-in for ultra-fast performance.

