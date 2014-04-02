---
layout: post
title: "Octopress: Migrate Disqus Comments from WordPress"
date: 2014-04-02 12:08:35 +0530
comments: true
categories:
  Blogging
  Technology 

---

Last week I migrated my blog from WordPress to Octopress and find it very cool blogging engine. Though, migraiton of posts and pages was quite easy but migration or you can say linking of Disqus comments from WorlPress pages to Octopress was difficult. I tried lot of different options but could not relink the Disqus comments.<!--more--> 


Then I read the Disqus documentation and advanced migration options, and I found a options using that we can relink our comments properly to new bloging urls.


If you read Tools option under Discussion tab, you will find a option "Upload a URL Map". There it says if your blog urls or slug was changed you can provide a CSV file which maps your old urls to new urls.( Left side old urls, Right side new urls)
In my case CSV file format was similar to given below format.


    http://vishnuatrai.com/?page_id=2, http://vishnuatrai.com/about/index.html
    http://vishnuatrai.com/?p=115, http://vishnuatrai.com/blog/2010/10/12/managed-rake-tasks-rake-migration
    http://vishnuatrai.com/?p=132, http://vishnuatrai.com/blog/2010/11/01/why-nosql
    http://vishnuatrai.com/?p=12, http://vishnuatrai.com/blog/2010/11/01/why-nosql


The Disqus Crawler runs and relink your comments to new urls.

### Reference
http://help.disqus.com/customer/portal/articles/286778-using-the-migration-tools
