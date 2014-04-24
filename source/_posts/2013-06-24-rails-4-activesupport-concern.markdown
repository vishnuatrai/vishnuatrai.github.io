---
layout: post
title: "Rails 4 - ActiveSupport::Concern"
date: 2013-06-24 18:00:23 +0530
comments: true
categories:
  Rails4
  Rails
  RoR 
---

Rails 4 ActiveSupport::Concern is a very cool feature to organize your module in such a way 
that when you include module in a class you don't need to write extra callbacks to extend or 
include methods in your class. This feature is a great way to keep your code organized and DRY. This will also handles module dependencies.
<!--more-->
You just need to require activesupport/concern and need to extend your module with ActiveSupport::Concern. 
When you include your module in a class, included block will be executed with in class 
context, ClassMethods block will be added to class itself and methods inside will become 
class methods, other methods outside included and ClassMethods block will be included as 
instance methods. eg.

    module Commentable
      extend ActiveSupport::Concern

      included do         # This block will be executed within class itself
        has_many :comments, as: :commentable, dependent: :destroy
        scope :with_comments, ->{ joins(:comments) }
      end 

      module ClassMethods # Methods within this black will be added to class methods
        def comment_search(params)
          .....
        end
      end

      def comment_titles  # This method will be added to instance methods
        comments.map(&:title) 
      end      
      def comment_users   # This method will be added to instance methods
        comments.map(&:username)
      end

    end

This should be your module structure and when you include your module in class it will work.
No method will break. eg.

    Class Post
      include Commentable
    end

    Post.with_comments  
    Post.comment_search({}) 

    p = Post.find(1)
    p.comments
    p.comment_titles
    p.comment_users

     

