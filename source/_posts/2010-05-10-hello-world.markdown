---
author: admin
comments: true
date: 2010-05-10 07:14:09+00:00
layout: post
slug: hello-world
title: Blocks in Ruby
wordpress_id: 1
categories:
- Technology
tag:
- Ruby
---

Blocks are one of Ruby’s great assets. Block's important quality is good code readability and simplicity.Blocks are the way to create a block of code grouped together. Blocks can take arguments and return values as normal functions. they are not executed where they defined they stored in a veriable passed as argument or return from a function.




For example :--




[ "Ruby", "Java", "PHP"].each { | lang | puts lang }




output --




Ruby




Java




PHP




in abouve example **each** runs the given block for each element of array.




here **lang** passed as argument in in block with in || bars and processed.




if you want you can pass more then one argument in blocks by comma seperated.




We can get a block from a function




for example --------




def get_block()




any_message = "Ruby blocks"




return Proc.new{  puts any_message }




end




c = get_block




c.call




**output---**




Ruby blocks
