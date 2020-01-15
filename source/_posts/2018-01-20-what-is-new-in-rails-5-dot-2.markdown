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
Active Storage supports modern approach for file uploading to Amazon S3, Google Cloud Storage, Microsoft Azure Cloud file storage. It will also provide references to active record database tables `active_storage_blobs` and `active_storage_attachments`. `rails active_storage:install` will install initial setup for active storage.<!--more-->Configure and setup `config/storage.yml` cloud credentials and storage buckets.

	#config/storage.yml
    local:
      service: Disk
      root: <%= Rails.root.join("storage") %>
    test:
      service: Disk
      root: <%= Rails.root.join("tmp/storage") %>
    amazon:
      service: S3
      access_key_id: ""
      secret_access_key: ""
      bucket: ""
      region: ""

###Redis Cache Store
Rails 5.2 ships with built-in Redis cache store. The Redis cache store takes advantage of Redis support for automatic eviction when it reaches max memory, allowing it to behave much like a Memcached cache server.

Finally, add the configuration in the relevant `config/environments/*.rb` file:


    config.cache_store = :redis_cache_store, { url: 'redis://redis-server:6379' }
 
###HTTP/2 Early Hints
This means we can automatically instruct the web server to send required style sheet and JavaScript assets early. Which means faster full page delivery.

To start the server with Early Hints enabled pass `--early-hints` to `rails server`

###Credentials
Added `config/credentials.yml.enc` file to store production app secrets. It allows saving any authentication credentials for third-party services directly in repository encrypted with a key in the `config/master.key` file or the `RAILS_MASTER_KEY` environment variable

To add new secret to credentials, first run rails secret to get a new secret. Then run rails credentials:edit to edit credentials, and add the secret. Running credentials:edit creates new credentials file and master key, if they did not already exist.
 
By default, this file contains the application's `secret_key_base`, but it could also be used to store other credentials such as access keys for external APIs.

The secrets kept in credentials file are accessible via `Rails.application.credentials`. For example, with the following decrypted `config/credentials.yml.enc`


  #config/credentials.yml.enc
    secret_key_base:<secret key base>
    api_client_key: <secret key1>
    api_client_secret: <secret key2>

###Content Security Policy
Content security policy can be configured as a global default policy and then override it on a per-resource basis and even use lambdas to inject per-request values into the header such as account subdomains in a multi-tenant application.

The HTTP `Content-Security-Policy` response header allows web site administrators to control resources the user agent is allowed to load for a given page. With a few exceptions, policies mostly involve specifying server origins and script endpoints.


Example -
  # config/initializers/content_security_policy.rb
    Rails.application.config.content_security_policy do |policy|
      policy.default_src :self, :https
      policy.font_src    :self, :https, :data
      policy.img_src     :self, :https, :data
      policy.object_src  :none
      policy.script_src  :self, :https
      policy.style_src   :self, :https

      # Specify URI for violation reports
      policy.report_uri "/csp-violation-report-path"
    end

Example controller overrides:

    # Override policy inline
      class PostsController < ApplicationController
        content_security_policy do |p|
          p.upgrade_insecure_requests true
        end
      end

    # Using literal values
      class PostsController < ApplicationController
        content_security_policy do |p|
          p.base_uri "https://www.example.com"
        end
      end

    # Disabling the global CSP
      class PagesController < ApplicationController
        content_security_policy false, only: :index
      end

<br/>
References: https://guides.rubyonrails.org/5_2_release_notes.html
 
