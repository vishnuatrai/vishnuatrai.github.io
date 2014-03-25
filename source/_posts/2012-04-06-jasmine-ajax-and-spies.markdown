---
author: admin
comments: true
date: 2012-04-06 05:42:46+00:00
layout: post
slug: jasmine-ajax-and-spies
title: 'Jasmine: Ajax and Spies'
wordpress_id: 328
categories:
- Technology
tag:
- BDD
- jasmine
- Javascript
---

In continuation of previous post for Jasmine, here I want to share my experience with Jasmine and Ajax. I want to show how cool it is with jasmine to test ajax calls in javascript. <!--more--> Jasmine spies can be used for mock the ajax call with fake data. Spy replaces the function it is spying on. For example my javascript function have an ajax call -
    
    
    function myprofiledetails(){
        $.ajax({
          type: "GET",
          url: "http://vishnuatrai.com/aboutme.php",
          success: function(json){
            $('#infodiv').html("Ruby On Rails development with jasmine");
          }
       });
    }
    



In spec file I can spy the ajax call in beforeEach function. And so it won't make a server call and return the fake data.

    
    
    describe("mock about me", function() {
    
      beforeEach(function() {
        //mocking ajax call with Jasmine Spies
        var fakeData = "Hi, I am doing Ruby On Rails development with jasmine";
        spyOn($, "ajax").andCallFake(function(params) {
          params.success(fakeData);
        });
      });
    
      it("Should have a Happy Face", function() {
        myprofiledetails();
        expect($('#infodiv')).toHaveText("Ruby On Rails development with jasmine");
      });
    });
    



Jasmine provides some useful methods to support spies. 

    
    
    <b>expect(x).toHaveBeenCalled() </b> 
    passes if x is a spy and was called
    
    <b>expect(x).toHaveBeenCalledWith(arguments)</b>   
    passes if x is a spy and was called with the specified arguments
    
    <b>expect(x).not.toHaveBeenCalled() </b>                       
    passes if x is a spy and was not called
    
    <b>expect(x).not.toHaveBeenCalledWith(arguments) </b>   
    passes if x is a spy and was not called with the specified arguments
    



Spies can be trained to respond in a variety of ways when invoked:


    
    
    <b>spyOn(x, 'method').andCallThrough():</b>
    spies on AND calls the original function spied on
    
    <b>spyOn(x, 'method').andReturn(arguments):</b>
    returns passed arguments when spy is called
    
    <b>spyOn(x, 'method').andThrow(exception): </b>
    throws passed exception when spy is called
    
    <b>spyOn(x, 'method').andCallFake(function): </b>
    calls passed function when spy is called
    



There is a detailed wiki page for spices on github https://github.com/pivotal/jasmine/wiki/Spies.
