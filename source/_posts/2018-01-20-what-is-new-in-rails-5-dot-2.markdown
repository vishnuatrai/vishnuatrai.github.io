---
layout: post
title: "What is new in Rails 5.2"
date: 2018-01-20 16:02:51 +0530
comments: true
description: What is new in Rails 5.2
keywords: Rails 5.2, Rails, RoR, Ruby on Rails
categories:
  Rails&nbsp;5.2
  Rails
  RoR 
---

###Active Storage
Action Cable can integrates websocket with rails application. Action Cable server can handle multiple connection instances. It has only one instance per websocket connection. The client websocker connection(consumer) can subscribe to multiple cable channels.<!--more-->For example Action Cable server can have a `ChatChannel` and an `AppearancesChannel` and a consumer(websocket) can subscribe to either one or both of channels.

	#Publisher Streams
	# app/channels/chat_channel.rb
	class ChatChannel < ApplicationCable::Channel
  		def subscribed
    		stream_from "chat_#{params[:room]}"
  		end
	end


###Redis Cache Store
Define an `attribute` on a model with type. It is not essential to have a database column with the custom model `attribute`. `attribute` can also be used to provide default values.

    # db/schema.rb
	create_table :profiles, force: true do |t|
  		t.decimal :gpa
  		t.string :name, default: "Full Name"
	end
 
###HTTP/2 Early Hints
Define an `attribute` on a model with type. It is not essential to have a database column with the custom model `attribute`. `attribute` can also be used to provide default values.

    # db/schema.rb
	create_table :profiles, force: true do |t|
  		t.decimal :gpa
  		t.string :name, default: "Full Name"
	end
 
###Credentials
Define an `attribute` on a model with type. It is not essential to have a database column with the custom model `attribute`. `attribute` can also be used to provide default values.

    # db/schema.rb
	create_table :profiles, force: true do |t|
  		t.decimal :gpa
  		t.string :name, default: "Full Name"
	end
 
###Content Security Policy
Define an `attribute` on a model with type. It is not essential to have a database column with the custom model `attribute`. `attribute` can also be used to provide default values.

    # db/schema.rb
	create_table :profiles, force: true do |t|
  		t.decimal :gpa
  		t.string :name, default: "Full Name"
	end

<br/>
References: https://guides.rubyonrails.org/5_2_release_notes.html
 
