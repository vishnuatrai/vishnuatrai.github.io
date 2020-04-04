---
layout: post
title: "What is new in Rails 6"
date: 2019-09-15 14:09:12 +0530
comments: true
description: What is new in Rails 6
keywords: Rails 6, Rails, RoR, Ruby on Rails
categories:
  Rails&nbsp;6
  Rails
  RoR 
---


####Rails 6 require Ruby version 2.5 or greater. Upgrade to at least rails 5.2 or later versions and make sure application run properly without any failure, then attempt to upgrade to 6.0, follow [upgrade guide](https://edgeguides.rubyonrails.org/upgrading_ruby_on_rails.html#upgrading-from-rails-5-2-to-rails-6-0).

###ApplicationRecord - Multiple databases support
Active Record supports switching between multiple databases with a minimal change in codebase but a major impact on multiple replica set db. With multiple db support, rails application can have a read-only version of your database to use in areas known for having slow queries, or if it need to write to different databases depending on which controller request.
This requires a change in `database.yml` setup, ie. 
`
	development:
  	  primary:
    	database: primary_db
    	user: rw_user
  	  primary_replica:
        database: primary_db
        user: ro_user
        replica: true
      animals:
        database: animals_db
        user: rw_user
      animals_replica
        database: animals_db
        user: ro_user
        replica: true
`
You can then specify at the model-level which database(s) you want to use:
	class Animal < ApplicationRecord
		connects_to database: { writing: :animals, reading: :animals_replica }
	end

And then it’s just one line of code to temporarily switch between databases inside a block!
	Aminal.connected_to(role: :reading) do
		#Slow queries in jobs can be performaed here
	end

###Action Mailbox
ActionMailbox provides a set of tools that will allow applications to route incoming mail into controller-like mailboxes for processing. Action Mailbox requires Active Job and Active Storage as part of it’s and a database table `InboundEmail`.
####Setup
	bin/rails action_mailbox:install
	bin/rails db:migrate
####Routing and Processing
	# app/mailboxes/application_mailbox.rb
	class ApplicationMailbox < ActionMailbox::Base
  		routing /^comment\+(.+)@example\.com/i => :comments
	end
	# app/mailboxes/comments_mailbox.rb
	class CommentsMailbox < ApplicationMailbox
  		def process
    		user = User.find_by(email: mail.from)
    		post_uuid = COMMENTS_REGEX.match(mail.to)[1]
    
    		post = Post.find_by(uuid: post_uuid)
    		post.comments.create(user: user, content: mail.body)
  		end
	end

###Action Text
ActionText is am implementation of rich-text support(Trix editor by Basecamp). Run bin/rails action_text:install to add the Yarn package and copy over the necessary migration. Also, you need to set up Active Storage for embedded images and other attachments. Both trix and @rails/actiontext should be required in your JavaScript pack.
	// application.js
	require("trix")
	require("@rails/actiontext")
It exposes a has_rich_text method that we can apply to models and Action Text will take care of the rest. 
	# app/models/article.rb
	class Article < ApplicationRecord
  		has_rich_text :content
	end

###Parallel Testing
Rails 6 adds parallel test to Rails applications by default. It parallelize test suite and enable faster test suite run times and efficiency. Configuration via parent test class
	class ActiveSupport::TestCase
		parallelize(workers: 2, with: :processes) # or :threads
	end
or through environment variable PARALLEL_WORKERS and it’ll create the database with a numbered suffix.
  	PARALLEL_WORKERS=2 rails test

###Action Cable Testing
Action Cable testing tools allow you to test your Action Cable functionality at any level: connections, channels, broadcasts. By default, when you generate new Rails application with Action Cable, a test for the base connection class (`ApplicationCable::Connection`) is generated as well under `test/channels/application_cable` directory.

###Webpack
`webpacker` gem, replacing the previously-default Rails Asset Pipeline and providing for better interoperability with modern JavaScript libraries and coding standards. Using webpacker gem, all StyleSheets, images and JS libraries wrap into a single bundle with a single access point.

###Zeitwerk
Zeitwerk is a new and improved, thread-safe code loader for Rails, Configuration to enable - 
	config.autoloader = :zeitwerk