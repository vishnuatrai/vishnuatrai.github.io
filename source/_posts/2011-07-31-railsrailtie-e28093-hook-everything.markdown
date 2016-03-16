---
author: admin
comments: true
date: 2011-07-31 12:03:44+00:00
layout: post
slug: railsrailtie-%e2%80%93-hook-everything
title: Rails::Railtie – Hook Everything
description: "Rails::Railtie – Hook Everything"
keywords: Rails&nbsp;3, Railtie, RoR, Ruby on Rails
categories:
- Rails&nbsp;3
- Railtie
- RoR
- Ruby on Rails
---

After Rails 3 released I have been trying to find major and important changes in rails framework over 2.x version. The change I like most is Rails Railtie. Using Railtie you can configure your own gem or plugin in a rails application according to your choice in boot process. <!--more-->  If you are a plugin developer, you can use Railtie class that is capable of loading generators, tasks, middlewares and adding subscribers.

Rails::Engine is nothing more than a Railtie and it has some initializers already set and Rails::Application and Rails::Plugin are also engines. Any subclass of Railtie can be use to initialize rails application, so your plugin can be a first class member of rails application.

Here is sample plugin Railtie that will be loaded when your rails application boots.

    
    require 'rails'
    class MyPlugin < Rails::Railtie
      # railtie code goes here
    end


Here I will give you some examples of Railtie methods, how to customize and extend your rails application using your plugin or gem library.


#### 1) generators


Rails will require any generators if you have require generator path in your Railtie class within generators block.

    
    
    generators do
      require 'lib/to/generator.rb'
    end
    




#### 2) rake_tasks


You can ship rake tasks of your plugin in rails application if you require in Railtie

    
    
    rake_tasks do 
      require 'lib/to/rake_task.rb'
    end
    




#### 3) initializer


Railtie initializer method can create initializers that can run during the Rails boot process. It has the options to run a initializer before or after a specific initializer.

    
    
    initializer "plugin_railtie.boot_plugin" do
      Plugin.boot(NewPlugin)
    end
     
    initializer "new_plugin_railtie.boot_new_plugin", :before => "plugin_railtie.boot_plugin" do
        NewPlugin.boot!
    end
    




#### 4) console


console method allows to add code that will run when Rails console starts.

    
    
     console do
       puts "my plugin loaded."
     end
    
