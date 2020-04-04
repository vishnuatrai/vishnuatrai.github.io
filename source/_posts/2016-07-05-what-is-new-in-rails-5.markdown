---
layout: post
title: "What is new in Rails 5"
date: 2016-07-05 17:34:19 +0530
comments: true
description: What is new in Rails 5
keywords: Rails 5, Rails, RoR, Ruby on Rails
categories:
  Rails&nbsp;5
  Rails
  RoR 
---
###ApplicationRecord
Similar to `ApplicationController` which is the common base class for all controllers that you get with new Rails apps, `ApplicationRecord` will provide a base class for your ActiveRecord models. This provides a common place to put any base model concerns.
`app/models/application_record.rb` file will be automatically added to models in Rails 5 applications.

	# app/models/application_record.rb
	class ApplicationRecord < ActiveRecord::Base
		self.abstract_class = true
	end

###ActionCable
Action Cable can integrates websocket with rails application. Action Cable server can handle multiple connection instances. It has only one instance per websocket connection. The client websocker connection(consumer) can subscribe to multiple cable channels.<!--more-->For example Action Cable server can have a `ChatChannel` and an `AppearancesChannel` and a consumer(websocket) can subscribe to either one or both of channels.

	#Publisher Streams
	# app/channels/chat_channel.rb
	class ChatChannel < ApplicationCable::Channel
  		def subscribed
    		stream_from "chat_#{params[:room]}"
  		end
	end

    #Subscriber
	// app/javascript/channels/chat_channel.js
	import consumer from "./consumer"
	consumer.subscriptions.create({ channel: "ChatChannel", room: "Best Room" })


###ActiveRecord::Attributes
Define an `attribute` on a model with type. It is not essential to have a database column with the custom model `attribute`. `attribute` can also be used to provide default values.

    # db/schema.rb
	create_table :profiles, force: true do |t|
  		t.decimal :gpa
  		t.string :name, default: "Full Name"
	end
 
	# app/models/profile.rb
	class Profile < ActiveRecord::Base
	end
 
	profile = Profile.new(gpa: '4.1')
 
	# before
	profile.gpa # => BigDecimal.new(4.1)
	profile.new.name # => "Full Name"
 
	class Profile < ActiveRecord::Base
  		attribute :gpa, :integer # custom type
  		attribute :name, :string, default: "Your Name" # default value
  		attribute :current_time, :datetime, default: -> { Time.now } # default value
  		attribute :field_without_db_column, :integer, array: true
	end
 
	# after
	profile.gpa # => 10
	Profile.new.name # => "Your Name"
	Profile.new.current_time # => 2015-05-30 11:04:48 -0600
	model = Profile.new(field_without_db_column: ["1", "2", "3"])
	model.attributes # => {field_without_db_column: [1, 2, 3]}


###Rails API - ActionController::API
To avoid middlewares used for browser based web applications and server public facing json APIs, we can create API only rails application here after 5.0. `rails new my_api --api` can be used to generate application which will be API only and will not generate assets and views.
	
	# app/controllers/application_controller.rb
    class ApplicationController < ActionController::API
    end


###Ruby 2.2.2+ required version from Rails 5.0
Ruby 2.2.2+ required version from Rails 5.0

<br />
<br />
###References
https://guides.rubyonrails.org/5_0_release_notes.html
