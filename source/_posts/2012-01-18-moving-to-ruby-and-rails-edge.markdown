---
author: admin
comments: true
date: 2012-01-18 09:55:34+00:00
layout: post
slug: moving-to-ruby-and-rails-edge
title: Moving to Ruby and Rails Edge
description: Moving to Ruby and Rails Edge
keywords: Rails&nbsp;3, Ruby, RubyOnRails, RoR, Ruby on Rails
categories:
- Rails&nbsp;3
- Ruby
- RubyOnRails
- RoR
---

Since our team always works with technology edge whether it is rails or ruby, from last week we started moving to ruby 1.9.3 and rails 3.1.3. I am sharing our experiences we found during migrating our product to rails and ruby latest release.<!--more-->


##### Ruby-1.9.3 Upgrade


Compare to ruby 1.8.7 in ruby 1.9.3 there are lot of changes, newly introduced, and things taken away, some of them are here -


#### 1) Load path not including the working directory



    
    require_relative or require './file'




#### 2)New Hash syntax – without hash-rockets(=>)



    
    { name: 'vishnu', age: '26'}




#### 3)Character Encoding – more encoding support for other char-sets


cp950, cp951, UTF-16, and UTF-32 etc.


#### 4)Array – to_s no longer joins elements, but returns and inspect style representation


choice method goes to sample method
method nitems removed


#### 5)Procs and Lambdas – proc {} creates a Proc object instead lambda



    
     proc {}.lambda?          #=> false
     lambda {}.lambda?        #=> true


source_location method gives file_name, line_number of proc


#### 6)Time – Time standard library changed


Time.parse uses dd/mm/yyyy format instead of mm/dd/yyyy
Time monday?, tuesday?, wednesday?, thursday?, friday?, saturday? sunday? Available to check a day


#### 7)New in Standard Library1.9.3


json, to_json and JSON.load(data) method is available with objects
minitest MiniTest and MiniTest::Spec
prime – Prime number library

    
    7.prime?          #=> true
    6.prime?          #=> false




#### 8)Removed from Standard Library 1.9.3


soap/wsdlDriver, date2, ftools,jcode(removed because now encoding support), parsedate, rubyunit


##### Rails-3.1.3 Upgrade


In order to upgrade to Rails 3.1.3 version with ruby 1.9.3 here are some points, those can help you -


#### 1)To support special characters in your ruby code


Put {# -*- coding: utf-8 -*-} beginning of the file and
configuration in application.rb for default encoding. config.encoding = "utf-8"


#### 2)Capybara should upgrade to 1.1.2 version and cucumber with 1.1.2 version


regenerate cucumber environment with required options

    
    bundle exec rails g cucumber:install --capybara –rspec




#### 3)By default ActiveResource uses JSON format


To make default XML format specify format in initializers

    
    ActiveResource::Base.format = ActiveResource::Formats::XmlFormat




#### 4)To load all helpers in each controller/action specify config



    
    config.action_controller.include_all_helpers = true   in application.rb
    instead
    helpers :all in application_controller.rb




#### 5)To load test_helper.rb in all test specs, use



    
    require './test/test_helper'
    instead
    require File.dirname(__FILE__) + '/../test_helper'




#### 6)RJS template fix


RJS template and rjs code in controller is not supported, changed to .html.js template. For a quick fix you can use prototype-rails gem in Gemfile

    
    gem 'prototype-rails'     #Gemfile


prototype-rails do not use prototype.js it provides helper methods to support rjs templates. later on you can migrate all .rjs to .js


#### 7) Enable assets pipeline


To enable assets pipeline, an import feature of rails 3.1, you need to set application.rb with configurations-

    
    config.assets.enabled = true
    config.assets.initialize_on_precompile = false


and production.rb with configurations -

    
    config.assets.compress = true
    config.assets.compile = true
    config.assets.digest = false
    config.assets.js_compressor  = :uglifier


and gemfile should have a group :assets and should include saas-rails, coffee-rails, uglifier gems.

    
    group :assets do
      gem 'sass-rails',   "~> 3.1.0"
      gem 'coffee-rails', "~> 3.1.0"
      gem 'uglifier'
    end
