---
author: admin
comments: true
date: 2012-03-12 08:54:24+00:00
layout: post
slug: jasmine-bdd-for-javascript
title: 'Jasmine: BDD for Javascript'
wordpress_id: 292
categories:
- BDD
- jasmine
- Javascript
- RubyOnRails
- RoR
---

In web development, if we are doing web 2.0 and modern web concepts, we have to write a lot of javascript and ajax calls and that will increase client side javascript day by day. <!--more--> We have lot of good javascript libraries for implementation. For faster delivery of features we need to cover the client side javascript with test cases. Jasmine is a tool to do BDD for javascript. It can mock ajax calls and you can use some static templates as a result. Jasmine also can be useful for TDD with javascript.



#### 1. Setting up a project


To set up a project for javascript BDD, first of all you need to add **'jasmine'** to your gem file. Then after bundle install you will have to run a generator {**rails g jasmine:install**} to generate jasmine defaults. Now you can run command **rake jasmine** to see your spec on browser. The server run default on 8888 port. You can see your specs by browsing http://localhost:8888. To write specs you can add a .js file in spec/javascripts/my_javascript_spec.js.



#### 2. Syntax 


Jasmine is very similar with rSpec in terms of syntax. It has methods like **describe** and **it**. Both functions take two arguments, first a string to show the context and second function to test the results. **describe** can be used for grouping related specs. 

    
    
    describe('addition', function () {  
        it('adds two numbers', function () {  
            expect(5 + 5).toEqual(10);  
        });  
    }); 
    





#### 3. Setup and Tear-down


Jasmine provides function **beforeEach** for setup the spec context and **afterEach** to clear the context. 

    
    
    describe("Spec setup and tear-down", function() {
      var number;
      beforeEach(function() {
        number = 0;
        number += 1;
      });
    
      afterEach(function() {
        number = 0;
      });
    
      it("first assertion", function() {
        expect(number).toEqual(1);              #green
      });
    
      it("second assertion", function() {
        expect(number).toEqual(1);              #green
      });
    });
    





#### 4. Matchers


Given few of the matches you can use for assertions - 

    
    
    toContain
       expect(["linux", "mac", "windows"]).toContain("linux");  
    toMatch
       expect(1050.50).toMatch(/\$\d*.\d\d/);
    toBeLessThan
       expect(1).toBeLessThan(2);  
    toBeGreaterThan
       expect(2).toBeGreaterThan(1);
    toBeTruthy
      expect(true).toBeTruthy(); 
    toBeFalsy
      expect(false).toBeFalsy();  
    
    and a lot many.
    


 


#### 5. Custom Matchers


We can write our own custom matcher using **this.addMatchers** in beforeEach call. 

    
    
    beforeEach(function () {  
      this.addMatchers({  
        toBeBetween: function (floor, ceiling) {  
          return this.actual > floor && this.actual < ceiling;  
        }  
      });  
    });  
    
