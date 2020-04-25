---
layout: post
title: "MySQL - Distributed GET_LOCK"
date: 2017-10-15 17:52:24 +0530
comments: true
description: MySQL - Distributed GET_LOCK
keywords: MySQL, Distributed Lock, Distributed GET_LOCK, GET_LOCK, DevOps
categories:
  MySQL
  MySQL&nbsp;Distributed&nbsp;Lock
  MySQL&nbsp;Distributed&nbsp;GET_LOCK
  MySQL&nbsp;GET_LOCK
  DevOps
---

Distributed global locking using `mysql get_lock()`, ensures releasing orphaned lock.

***`GET_LOCK(str,timeout)`*** <br /> 

Tries to obtain a lock with a name given by the string `str`, using a timeout of `timeout` seconds. A negative `timeout` value means infinite timeout. The lock is exclusive. While held by one session, other sessions cannot <!--more--> obtain a lock of the same name.

Returns `1` if the lock was obtained successfully, `0` if the attempt timed out (for example, because another client has previously locked the name), or `NULL` if an error occurred (such as running out of memory or the thread was killed with mysqladmin kill).

A lock obtained with `GET_LOCK()` is released explicitly by executing `RELEASE_LOCK()` or implicitly when your session terminates (either normally or abnormally). Locks obtained with `GET_LOCK()` are not released when transactions commit or roll back.


###USE CASE 1: Eliminate SPOF of background jobs or scheduled/cron job

###USE CASE 2: A process that allowed to run only once on a given time however the process is deployed on multiple hosts as part of different micro-services, ie. scheduled jobs

<br />

####Sample Java snippets
	import java.sql.DriverManager;
	import java.sql.Connection;
	import java.sql.PreparedStatement;
	import java.sql.ResultSet;
	import java.sql.SQLException;
	.....
	Connection connection = DriverManager.getConnection("jdbc:mysql://localhost/" + dbName, "root", "");
	PreparedStatement sth = connection.prepareStatement("SELECT GET_LOCK(?, ?)")
	sth.setString(1, "distributed_lock");
	sth.setInt(2, 2);

	ResultSet resultSet = sth.executeQuery();
    resultSet.next();
    Integer status = resultSet.getInt(1);

    if (resultSet.wasNull()) {
       throw new Exception("Can not obtain lock");
    }
    if (status == 0) {
      throw new Exception("Already locked `distributed_lock`");
    }
    if (status == 1) {
        //successfully get lock.. do something here ...
    }
    .....


Reference - [MySQL GET_LOCK](https://dev.mysql.com/doc/refman/5.7/en/locking-functions.html#function_get-lock)
