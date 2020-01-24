---
layout: post
title: "MySQL - Encryption at rest"
date: 2020-01-11 18:20:46 +0530
comments: true
description: MySQL - Encryption at rest
keywords: MySQL, Data Encryption at rest, DevOps
categories:
  MySQL
  Data&nbsp;Encryption&nbsp;at&nbsp;rest
  DevOps
---

Without encryption of data at rest, system role with access to file system can view data even without proper database permissions. A proper authentication can be used to protect data used by an application but data sitting in file system has been area of risk. 

With encryption at rest, it has been possible to protect data in transit when moving over the network. Data at Rest Encryption is a requirement for HIPAA, PCI regulations. <!--more-->


###Data Files at Risk
When insert queries run to store data in tables, the data will be stored in respective `dataDir` ie `/usr/local/var/mysql`. A system user that has filesystem access can use `strings` or `xxd` commands to view content in data files, bin logs or redo logs.

One way to avoid this risk either not to store sensitive data in database or encrypt sensitive data columns or encrypt the file system itself.

Other way is to implement Data encryption at rest. 


###Implement Data Encryption at rest, MySQL

Frist step to create a key using `openssl` command

	openssl enc -aes-256-cbc -md sha1 -k <enc password or passphase> -in keys.txt -out mysql.enc

Second, edit `/etc/my.cnf` and provide encryption configurations

	plugin-load-add=file_key_management.so
	file-key-management-filename=/var/lib/mysql/keys.txt
	innodb-encrypt-tables
	innodb-encrypt-log
	encrypt-binlog


`plugin-load-add` option will load the encryption plugin on mysql start.<br/>
`file-key-management-filename` option to provide reference to encryption key file.<br/>
`innodb-encrypt-tables` option will encrypt tables by default.<br/>
`innodb-encrypt-log` option will encrypt redo logs by default.<br/>
`encrypt-binlog` option will encrypt binlogs. 



Third, restart mysql
	
	service mysql restart

Fourth, enable the same options on the replication slaves

Fifth, verify tables are encrypted using below mysql query 
	
	select * from information_schema.INNODB_TABLESPACES_ENCRYPTION where ENCRYPTION_SCHEME=1;

<br/>

### innodb-encrypt-tables modes


Three ways to encrypt tables<br/>

1. `innodb-encrypt-tables` option in `my.cnf` will encrypt all tables unless table creation statement provided "encrypted=no" <br/>
2. `innodb-encrypt-tables` option in `my.cnf` will encrypt all tables and will not allow table creation with "encrypted=no" <br/>
3. `innodb-encrypt-tables` option is not provided in `my.cnf` but the plugin presents, it can explicitely encrypt a table by including "encrypted=yes" in table creation statement.<br/>


###Performance Overhead
~10% to ~20% performance overhead obeserved in previous MySQL deployments.

Database-level encryption can be a better option than the filesystem-level encryption if the application can not be changed.



