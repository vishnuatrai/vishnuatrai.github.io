---
layout: post
title: "Ruby 2.1 Changelog"
date: 2014-04-29 12:01:04 +0530
comments: true
categories: 
- Ruby
- Ruby 2.1
---

Ruby 2.1.0 has been released now question is, what's new in ruby 2.1.0. Below list of new 
features introduced in ruby 2.1.0.
### 1) def's return value <!--more-->
In earlier versions it was nil but in ruby 2.1 it will return a symbol.

    irb(main):002:0> def my_method
    irb(main):003:1> end
    => :my_method

### 2) Rational Number and Complex Number Literals
Earlier version you will have to use core classes to use relational and complex numbers, 
ruby 2.1 gives r and i suffix for them. Older version will give error for these literals.

    irb(main):003:0> (2+3i) + Complex(5, 4i)
    => (3+3i)

### 3) Required Keyword arguments
Keyword arguments introduced by ruby 2.0 and there was no way to make them required. 
In ruby 2.1 for missing keyword arguments it will raise 
ArgumentError: missing keyword: keyword

    def permaliinkify(str:, delimiter: "-")
      str.downcase.split.join(delimiter)
    end

    irb(main):002:0> permalinkify(delimiter: "-lol-")
    ArgumentError: missing keyword: str
    from (irb):LN
    from /.rvm/2.1.0/bin/irb:11:in `<main>'

### 4) Restricted Generational Garbage Collector (RGenGC)
Ruby 2.1 introduced faster garbage collector RGenGC. That will make mark and sweep faster.
RGenGC design of garbage collector leverages the fact that most objects collected 
by the garbage collector were the objects most recently created.
### 5) Exception#cause
Ruby 2.1 provides a way to have an exception carry a "cause" along with it.

    begin
      begin
        raise "Error!"
      rescue => e
        raise StandardError, "take the cause"
      end
    rescue Exception => e
      puts "Caused by  : #{e.cause.message} [#{e.cause.class}]"
    end
    => Caused by  : Error! [RuntimeError]

### 6) Refinements
In ruby 2.1, Refinements provide an alternate way to scope our modifications in 
ruby open classes or modules. 

    module Permalinker
      refine String do
        def permalinkify
          downcase.split.join("-")
        end
      end
    end
 
    class Post
      using Permalinker
      def initialize(title)
        @title = title
      end
      def permalink
        @title.permalinkify
      end
    end
    irb(main):002:0> post = Post.new("Ruby on Rails")
    irb(main):002:0> post.permalink
    => "ruby-on-rails"
    irb(main):023:0> "ruby on rails".permalinkify
    NoMethodError: undefined method `permalinkify' for "ruby on rails":String

