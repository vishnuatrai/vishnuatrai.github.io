---
layout: post
title: "What is new in Rails 4.2"
date: 2015-01-15 17:58:43 +0530
comments: true
description: What is new in Rails 4.2
keywords: Rails 4.2, Rails, RoR, Ruby on Rails
categories:
  Rails&nbsp;4.2
  Rails
  RoR 
---

###Active Job
Active Job is a framework for declaring jobs and making them run on a variety of queueing backends. These jobs can be everything from regularly scheduled clean-ups, to billing charges, to mailings. Anything that can be chopped up into small units of work and run in parallel.

    class GuestsCleanupJob < ActiveJob::Base
      queue_as :default
 
      def perform(*guests)
        # Do something later
      end
    end

    # Enqueue a job to be performed as soon the queuing system is free.
    GuestsCleanupJob.perform_later guest

    #Setting the Backend
    # config/application.rb
    module YourApp
      class Application < Rails::Application
        # Be sure to have the adapter's gem in your Gemfile and follow
        # the adapter's specific installation and deployment instructions.
        config.active_job.queue_adapter = :sidekiq
      end
    end

###Asynchronous Mails
Building on top of Active Job, Action Mailer now comes with a `deliver_later` method that sends emails via the queue, so it doesn't block the controller or model if the queue is asynchronous (the default inline queue blocks).

Sending emails right away is still possible with `deliver_now`.

###Adequate Record
Adequate Record is a set of performance improvements in Active Record that makes common find and find_by calls and some association queries up to 2x faster.

It works by caching common SQL queries as prepared statements and reusing them on similar calls, skipping most of the query-generation work on subsequent calls.

    Post.find(1)  # First call generates and cache the prepared statement
    Post.find(2)  # Subsequent calls reuse the cached prepared statement
 
    Post.find_by_title('first post')
    Post.find_by_title('second post')
 
    Post.find_by(title: 'first post')
    Post.find_by(title: 'second post')
 
    post.comments
    post.comments(true)

Caching is not used in the following scenarios:
    - The model has a default scope
    - The model uses single table inheritance
    - find with a list of ids, eg.
        Post.find(1, 2, 3)
        Post.find([1,2])
    - find_by with SQL fragments, eg.
        Post.find_by('published_at < ?', 2.weeks.ago)

###Web Console
Web Console adds an interactive Ruby console on every error page and provides a console view and controller helpers.

The interactive console on error pages lets you execute code in the context of the place where the exception originated. The console helper, if called anywhere in a view or controller, launches an interactive console with the final context, once rendering has completed.

###Foreign Key Support
The migration DSL now supports adding and removing foreign keys. They are dumped to `schema.rb` as well. At this time, only the `mysql`, `mysql2` and `postgresql` adapters support foreign keys.

    # add a foreign key to `articles.author_id` referencing `authors.id`
    add_foreign_key :articles, :authors
 
    # add a foreign key to `articles.author_id` referencing `users.lng_id`
    add_foreign_key :articles, :users, column: :author_id, primary_key: "lng_id"
 
    # remove the foreign key on `accounts.branch_id`
    remove_foreign_key :accounts, :branches
 
    # remove the foreign key on `accounts.owner_id`
    remove_foreign_key :accounts, column: :owner_id

###References
http://guides.rubyonrails.org/4_2_release_notes.html


