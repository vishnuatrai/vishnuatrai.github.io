---
author: admin
comments: true
date: 2012-04-06 05:42:46+00:00
layout: post
slug: jasmine-ajax-and-spies
title: 'Jasmine: Ajax and Spies'
description: "Jasmine: Ajax and Spies"
keywords: BDD, jasmine, Javascript
categories:
- BDD
- jasmine
- Javascript
---

In continuation of previous post for Jasmine, here I want to share my experience with Jasmine and Ajax. I want to show how cool it is with jasmine to test ajax calls in javascript. <!--more--> Jasmine spies can be used for mock the ajax call with fake data. Spy replaces the function it is spying on. For example my javascript function have an ajax call -
    
    
    function myprofiledetails(){
        $.ajax({
          type: "GET",
          url: "http://vishnuatrai.in/aboutme.php",
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

    
    
    expect(x).toHaveBeenCalled()
    passes if x is a spy and was called
    
    expect(x).toHaveBeenCalledWith(arguments)
    passes if x is a spy and was called with the specified arguments
    
    expect(x).not.toHaveBeenCalled()                       
    passes if x is a spy and was not called
    
    expect(x).not.toHaveBeenCalledWith(arguments)   
    passes if x is a spy and was not called with the specified arguments
    



Spies can be trained to respond in a variety of ways when invoked:


    
    
    spyOn(x, 'method').andCallThrough():
    spies on AND calls the original function spied on
    
    spyOn(x, 'method').andReturn(arguments):
    returns passed arguments when spy is called
    
    spyOn(x, 'method').andThrow(exception):
    throws passed exception when spy is called
    
    spyOn(x, 'method').andCallFake(function): 
    calls passed function when spy is called
    



There is a detailed wiki page for spices on github https://github.com/pivotal/jasmine/wiki/Spies.
