---
author: admin
comments: true
date: 2010-10-12 09:59:48+00:00
layout: post
slug: managed-rake-tasks-rake-migration
title: 'Rake Migration: Track Rake Tasks with versions'
description: "Rake Migration: Track Rake Tasks with versions"
keywords: Ruby, Rake Task, Migration
categories:
- Ruby
- Rake Task
- Migration
---

I have started working on a plugin which helps rails projects to maintain the list of rake tasks.

We are working on a project there are four to six teams at different geo-locations Bangalore, Kolkata, Edmonton etc.For the same project we have different environments like Developemnt, Testing, Staging, QA, QA2, Beta etc.All teams are working with new features, as well as bug fixes.<!--more-->

Many times we found a situation that a specific rake task should be run on a specific environment to fix the data. A lot of time team members forgot to shoot a mail and tell every team about a rake task which will fix a bug on specific environment. As other people update code base and start using the application they got stuck and it take a lot of time to resolve the issue that a specific task was not run because team did not get any information as such.

To resolve this issue we got a idea form rake tasks to migrate database (db:migrate). So if we can manage rake tasks with versions and if we can keep track of tasks those have been run, and yet to run on system. One can generate a ruby file write there any ruby code or invoke any rake task, and upload to repository other people only take update from repo and run a simple commend and the system will update.

Here we don’t have to worry to inform everybody about new task to fix bug or update system.

Here is the URL for plugin

[http://github.com/vatrai/rake_migration](http://github.com/vatrai/rake_migration)

By using generators one can generate blank ruby files prefix with time-stamped version.  Version helps to keep track of tasks that are **Run**, **yet to Run** on the system. One can specify Rails environment in ruby files, specify what data fix should be run on a specified environment using simple **if** condition.

To generate simple ruby file use:

** ruby script/generate rake_migration <file_name>**

This command creates a blank ruby file in** {RAILS_ROOT}/rake/migrate **directory.  Write ruby code or invoke rake task in the generated ruby file.

To run generated files run rake command:

**{RAILS_ROOT} rake rake_migration:migrate**

This command runs remaining(yet to run) ruby files in **{RAILS_ROOT}/rake/migrate** and saves version in rake_migrations table in database.
