---
layout: post
title: "Elasticsearch - mapper_parsing_exception, failed to parse field"
date: 2017-12-20 15:37:59 +0530
comments: true
description: Elasticsearch - mapper_parsing_exception, failed to parse field
keywords: Elasticsearch
categories:
  Elasticsearch
---

We have recently faced issue with Elasticsearch field types and mapping, when if a field is mapped with a type other type can not be indexed for the same field. Elasticsearch indesing mechanism ristrict that. for example we have created a index named `users` and inserted a record with `text` type field `name`. <!--more-->

![](/images//posts/elasticsearch1.png)

Second record we want to insert same field with another type per say `object`, it will raise exceptation `mapper_parsing_exception, failed to parse field`.

![](/images//posts/Elasticsearch2.png)

Because with insertation of first record the index will create a mapping and provide the field type `text` with second record insertation where the type is an object it will not match with the mapping and raise exceptation. 


![](/images//posts/elasticsearch3.png)

###Simple Hack But Many pitfalls
Since by nature Elasticsearch doesn't support above scenario, once the mapping is created and type is assigned to a field it will not allow indexing of other type. However in my case we have to store the data in elasticsearch engine, so converted the object to string and that worked. But it will not support query on the converted object and on client side also we will need to convert back to object type.

![](/images//posts/elasticsearch4.png)

After converting the object to text it will indexed as a text field only and will look like a escaped string object.


![](/images//posts/elasticsearch5.png)

Still investigating on better solution or way to work on this issue.