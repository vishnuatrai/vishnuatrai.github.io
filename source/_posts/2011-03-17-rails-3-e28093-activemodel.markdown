---
author: admin
comments: true
date: 2011-03-17 17:25:31+00:00
layout: post
slug: rails-3-%e2%80%93-activemodel
title: Rails 3 – ActiveModel Basics
wordpress_id: 147
categories:
- Rails3
- RoR
- Ruby
- RubyOnRails
---

Hey Guys,

It is long time to write something, since I was busy with some open-source stuff. Over last two weeks we were migrating an application from rails 2.x version to rails 3.x. In that application we had lot of ActiveResource classes and other classes, and in those classes we need to validate some attributes and also we need to wrap call backs around some methods in ActiveRecord style. <!--more--> In earlier 2.x rails version we had to write some code for validation and  call back that was written us.

During the migration from 2.x version to rails 3, we got to know about ActiveModel module that will give you lot of features in ActiveRecord style.

Here, I am sharing my experiences with ActiveModel -

ActiveModel is a nice library provided by Rails 3, by using that any ruby object can behave like a ActiveRecord class object. Some of ActiveModel API explained below -


#### 1.1 AttributeMethods


AttributeMethods module can add custom prefixes and suffixes on  methods of a class. It is used by defining the prefixes and suffixes,  which methods on the object will use them.

    
    class Person
      include ActiveModel::AttributeMethods
    
      attribute_method_prefix 'reset_'
      attribute_method_suffix '_highest?'
      define_attribute_methods ['age']
    
      attr_accessor :age
    
    private
      def reset_attribute(attribute)
        send("#{attribute}=", 0)
      end
    
      def attribute_highest?(attribute)
        send(attribute) > 100 ? true : false
      end
    
    end
    
    person = Person.new
    person.age = 110
    person.age_highest?  # true
    person.reset_age     # 0
    person.age_highest?  # false




#### 1.2 Callbacks


Callbacks gives Active Record style callbacks. This provides the  ability to define the callbacks and those will run at appropriate time.  After defining a callbacks you can wrap with before, after and around  custom methods.

    
    class Person
      extend ActiveModel::Callbacks
    
      define_model_callbacks :update
    
      before_update :reset_me
    
      def update
        _run_update_callbacks do
          # This will call when we are trying to call update on object.
        end
      end
    
      def reset_me
        # This method will call when you are calling update on object as a before_update callback as defined.
      end
    end










#### 1.3 Conversion








If a class defines persisted? and id methods then you can include  Conversion module in that class and you can able to call Rails  conversion methods to objects of that class.

    
    class Person
      include ActiveModel::Conversion
    
      def persisted?
        false
      end
    
      def id
        nil
      end
    end
    
    person = Person.new
    person.to_model == person  #=> true
    person.to_key              #=> nil
    person.to_param            #=> nil




#### 1.4 Dirty


An object becomes dirty when an object is gone through one or more  changes to its attributes and not yet saved. This gives the ability to  check whether an object has been changed or not. It also has attribute  based accessor methods. Lets consider a Person class with attributes  first_name and last_name

    
    require 'rubygems'
    require 'active_model'
    
    class Person
      include ActiveModel::Dirty
      define_attribute_methods [:first_name, :last_name]
    
      def first_name
        @first_name
      end
    
      def first_name=(value)
        first_name_will_change!
        @first_name = value
      end
    
      def last_name
        @last_name
      end
    
      def last_name=(value)
        last_name_will_change!
        @last_name = value
      end
    
      def save
        @previously_changed = changes
      end
    
    end










##### 1.4.1 Querying object directly for its list of all changed attributes.



    
    person = Person.new
    person.first_name = "First Name"
    
    person.first_name #=> "First Name"
    person.first_name = "First Name Changed"
    
    person.changed? #=> true
    
    #returns an list of fields arry which all has been changed before saved.
    person.changed #=> ["first_name"]
    
    #returns a hash of the fields that have changed with their original values.
    person.changed_attributes #=> {"first_name" => "First Name Changed"}
    
    #returns a hash of changes, with the attribute names as the keys, and the values will be an array of the old and new value for that field.
    person.changes #=> {"first_name" => ["First Name","First Name Changed"]}










##### 1.4.2 Attribute based accessor methods


Track whether the particular attribute has been changed or not.

    
    #attr_name_changed?
    person.first_name #=> "First Name"
    
    #assign some other value to first_name attribute
    person.first_name = "First Name 1"
    
    person.first_name_changed? #=> true


Track what was the previous value of the attribute.

    
    #attr_name_was accessor
    person.first_name_was  #=> "First Name"










##### Track  both previous and current value of the changed attribute. Returns an array if changed else returns nil



    
    #attr_name_change
    person.first_name_change #=> ["First Name", "First Name 1"]
    person.last_name_change #=> nil




























#### 1.5 Validations


Validations module adds the ability to class objects to validate them in Active Record style.







    
    class Person
      include ActiveModel::Validations
    
      attr_accessor :name, :email
    
      validates :name, :presence => true
      validates_format_of :email, :with => /^([^\s]+)((?:[-a-z0-9]\.)[a-z]{2,})$/i 
    
    end
    
    person = Person.new
    person.valid?                        #=> false
    person.name  = 'vishnu'
    person.email  = 'me'
    person.valid?                        #=> false
    person.email = 'me@vishnuatrai.com'
    person.valid?                        #=> true






