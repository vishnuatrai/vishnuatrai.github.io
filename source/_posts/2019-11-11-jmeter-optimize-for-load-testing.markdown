---
layout: post
title: "jMeter - Optimize for load testing"
date: 2019-11-11 17:40:57 +0530
comments: true
description: jMeter - Optimize for load testing
keywords: jMeter, DevOps
categories:
  jMeter
  DevOps
---

These are the jMeter optimization steps we have taken to simulate 1M request per second concurrency test for REST application deployed on Kubernetes cluster.

###Use a High Capacity Jenkins Slave to run jMeter jmx script
We have added a high capacity Jenkins slave node to run only jMeter performance test and have done jMeter installation on that server. All performance test jobs are restricted to run on that labeled slave node.<!--more-->
We can use available memory and CPU to optimize jMeter configuration.

###Run jMeter in non-GUI Mode
Apart from developing jmx script or debugging request/response not use GUI mode to run load test. In GUI mode AWT Event Thread will disrupt both your test and JMeter in case of more or less high load. GUI mode consumes a lot of memory and other resources, which in turn negatively impacts your scripts and tests. Using the non-GUI mode of JMeter helps to reduce both resource requirements and potential errors.

###Increase the Java Heap Size
Run JMeter with higher value of memory.
	`JVM_ARGS="-Xms512m -Xmx512m" jmeter.sh`

###Avoid Listeners
Avoid UI listeners like graphs or results table to avoid OutOfMemory issues. Preferably only write results to a JTL.

###Minimize Metrics Need to Store
Configure JMeter to ensure that it will only save the metrics that you absolutely need. You can control what to store by adding relevant lines to the user.properties file in jMeter installation.

###Generate Reports AFTER the Run
It takes resources to be written (CPU and memory) and for analysis in XML format. Use the outputted .jtl files to create reports once the load test is finished. Building the report requires a great amount of CPU and memory resources.

###Tweak the JVM
Settings like garbage collector (-XX:+UseConcMarkSweepGC), server JVM (-server) can be set inside JVM_ARGS by editing the JMeter launcher script.

`NEW="-XX:NewSize=128m -XX:MaxNewSize=128m"` line in the JMeter command script should match with values provided in HEAP.

`-XX:+UseConcMarkSweepGC` - this forces the usage of the CMS garbage collector. It will lower the overall throughput but leads to much shorter CPU intensive garbage collections.

`-server` - this switches JVM into “server” mode with runtime parameters optimization. In this mode JMeter starts more slowly, but your overall throughput will be higher
