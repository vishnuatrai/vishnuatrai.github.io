---
layout: post
title: "Ruby for Multi-tenant SOA platform"
date: 2013-08-02 18:03:45 +0530
comments: true
categories:
  RoR
  SOA
  Multitenant
 
---

After cloud computing, Multi-tenancy and SOA become a buzzword for tech companies those provide B2B solutions. 

Multi-tenancy will reduce the cost and simplifies the release management.<!--more--> 

On the other hand, SOA is to divide single monolithic application to multiple loosely coupled self contained independent applications aka services. SOA will benefit to develop new functions rapidly and scale platform in specific areas.

### Tools & Tips for Multi-tenancy 

Database seperation for each client we are using apartment gem. That gives very easy interface
to manage seperate databases for different clients. To run background tasks we are using 
apartment-sidekiq gem. That will automatically take care of database connection for a background
job initiated. 

You add these gems in your Gemfile and good to go.

    gem 'apartment'
    gem 'apartment-sidekiq'
   
### Tools and Tips for SOA (Service Oriented Architucture)

To expose APIs you can use ruby lightwaight framework sinatra. ActiveRecord for ORM to backend
You can use rspec for testing framework. Sometimes you make multiple requests to different 
services for a action, in these scenerios you can make parallel requests using typhoeus gem.
That will make a block call, get the response from different services, process those 
responses and give back the result to end user. In your Gemfile you can add typhoeus.

    gem 'sinatra'
    gem 'activerecord'
    gem 'typhoeus'

You can use JSON responses for all your APIs.



