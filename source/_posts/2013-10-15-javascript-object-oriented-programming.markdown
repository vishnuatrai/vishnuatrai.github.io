---
layout: post
title: "Javascript Object Oriented Programming"
date: 2013-10-15 18:00:18 +0530
comments: true
categories:
  Javascript
  OOJS 
---

JavaScript is an prototype-based OOP language in which behaviour reuse 
(inheritance) is achieved using existing objects which serve as prototypes. Whenever you 
assign a object as prototype of other object, properties and methods will be available to 
that object. <!--more-->You can override method or property in child object.


"Everything" in JavaScript is an Object. JavaScript allows you to define your own objects.
Objects are just data, with added properties and methods. This looks something similar to 
ruby for me. Also root level prototype of a object is "Object" and prototype of "Object" 
is null. 

In this type of OOP languages there is no existences of classes like java. c#, ruby etc.
Here you create objects and inheritance you can achieve using prototype assignment. 
Since only one prototype you can assign to a object so Javascript dosen't provide multiple
inheritence. Using prototype assignment chain to nexted level, it will provide multi-level 
inheritence. eg

    # A super class animal
    function Animal(){
      this.legs = 0,
      this.word = '',
      this.walk = function(){
        var i = 0, s = '';
        for(; i < this.legs; i++){
          s += ' step '
        }
        console.log(s);
      },
      this.speak = function(){
        console.log(this.word);
      }
    };

    a = new Animal();
    a.walk()               => 
    a.speak()              => 

    # A child class human
    function Human(){
      this.legs = 2,
      this.word = 'Hi'
    };
    
    Human.prototype = new Animal();
    
    h = new Human();
    h.walk()                => step step 
    h.speak()               => Hi









