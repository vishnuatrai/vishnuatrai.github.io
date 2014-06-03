---
layout: post
title: "What is new in Rails 4.1"
date: 2014-05-31 19:59:28 +0530
comments: true
categories:
  Rails4.1
  Rails4
  Rails
  RoR 
---

Rails 4.1 is a minor release but includes interesting features below - 

###Spring
Spring is new application preloader(like spork and zeous) available by default<!--more--> 
to rails app. Tests, rake and generators will be running much faster on large apps.

###Secrets
New file config/secrets.yml for your sensitive data. It contains rails app default 
secrets but you can add your secret keys here.

###Action Pack Variants
Render different views, allows to have different templates and action responses for 
the same mime type (say, HTML), even behave differently for phones, tablets and 
desktop browsers. You can now have individual templates for the desktop, tablet, 
and phone views while sharing all the same controller logic. 

###ActionMailer Previews
Sort of like LetterOpener and MailPreview, provide a way to visually see how emails 
look by visiting a special URL that renders them. The preview is available in ie. 
http://localhost:3000/rails/mailers/notifier/welcome, and a list of them in 
http://localhost:3000/rails/mailers. By default, these preview classes live in 
test/mailers/previews. This can be configured using the preview_path option.

###ActiveRecord enums
Declare an enum attribute where the values map to integers in the database, but can 
be queried by name. The good: Faster! Probably. It stores integers instead of strings. 
You won't notice.

    class Conversation < ActiveRecord::Base
      enum status: [ :active, :archived ]
    end

    conversation.archived!
    Conversation.statuses # => { "active" => 0, "archived" => 1 }    

###Message Verifiers
Message verifiers can be used to generate and verify signed messages. This can be 
useful to safely transport sensitive data like remember-me tokens and friends.
The method Rails.application.message_verifier returns a new message verifier that 
signs messages with a key derived from secret_key_base and the given message verifier name:

    signed_token = Rails.application.message_verifier(:remember_me).generate(token)
    Rails.application.message_verifier(:remember_me).verify(signed_token) # => token

    Rails.application.message_verifier(:remember_me).verify(tampered_token)
    # raises ActiveSupport::MessageVerifier::InvalidSignature


###References
http://weblog.rubyonrails.org/2014/4/8/Rails-4-1/

http://edgeguides.rubyonrails.org/4_1_release_notes.html

