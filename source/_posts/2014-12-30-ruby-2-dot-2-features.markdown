---
layout: post
title: "Ruby 2.2.0 Features"
date: 2014-12-30 17:53:04 +0530
comments: true
description: Ruby 2.2.0 Features
keywords: Ruby, Ruby 2.2
categories:
  Ruby
  Ruby&nbsp;2.2
---

### 1) Incremental and Symbol GC (RIncGC)
Ruby 2.2.0 release includes several grabage collection (GC) improvements. Symbols are now garbage collectable. 

Following the introduction of generational garbage collection in Ruby 2.1.0, which markedly improved the GC throughput, Ruby maintainers continue to introduce important changes in this space. The generational GC (RGenGC) classifies objects into generations, on the assumption that most objects die young. <!--more--> This assumption allows for high throughput and low pause time on younger objects, because older objects are only evaluated for deletion when there is no memory. But this means that older objects still suffer from high pause time.

The incremental GC (RIncGC), built on top of the generational GC, aims to cut that pause time while maintaining the same throughput. It achieves the shorter pause time by interleaving the mark phase, where objects are marked for GC, with Ruby's regular execution. Before Ruby 2.2.0, the mark phase was done in one big step.

### 2) Rails 5.0 support 
Rails 5.0 will target Ruby 2.2+ exclusively. There are a bunch of optimizations coming in Ruby 2.2 that are going to be very nice, but most importantly for Rails, symbols are going to be garbage collected. This means we can shed a lot of weight related to juggling strings when we accept input from the outside world. It also means that we can convert fully to keyword arguments and all the other good stuff from the latest Ruby.

### 3) Binding#local_variables
To know the local variables defined in a scope use `binding.local_variables` that will give all variables in use.

    def m1(a, b)
      puts binding.local_variables
      c = a + b
      puts binding.local_variables
      c
    end

    irb(main):001> m1(2,3)
    [:a, :b, :c]
    [:a, :b, :c]
    =>5

### 4) Binding#receiver
Ruby 2.2 provides a way to know which object is receiving the method call using `binding.receiver` method

    class Amimal
      def self.walk
        puts 'walk'
        binding.receiver
      end 
    end

    irb(main):001> Amimal.walk
    walk
    =>Animal
    
    class Dog < Animal
    end 

    irb(main):002> Dog.walk
    walk
    =>Dog

### 5) Enumerable#slice_after
This method is a complement to the existing `slice_before` method.

As the name suggests, `slice_before` is used to slice and dice enumerables. Given a way to match an element in the enumerable, it will find a match and cut it apart just prior to the match. 

    irb(main):001>[1, 'a', 2, 'b', 'c', 3, 'd', 'e', 'f'].slice_before { |e| e.is_a?(Integer) }.to_a
    =>[[1, "a"], [2, "b", "c"], [3, "d", "e", "f"]]

`slice_after` does slices after instead:

    irb(main):002>[1, 'a', 2, 'b', 'c', 3, 'd', 'e', 'f'].slice_after(Integer).to_a
    =>[[1], ["a", 2], ["b", "c", 3], ["d", "e", "f"]]

### 6) Enumerable#slice_when
A particularly fun addition is `slice_when`. Unlike `slice_after`, this method only accepts a block. It walks an enumerable, passing pairs of elements to the block. When the block returns `true`, the enumerable is sliced between the pair of elements:
    
    irb(main):001>[1, 3, 4, 5, 7, 8, 9, 10, 12].slice_when { |a, b| a + 1 != b }.to_a
    =>[[1], [3, 4, 5], [7, 8, 9, 10], [12]]

### 7) Float#next_float, Float#prev_float
These functions return the next or previous representable float. Note the word “representable” in that sentence, not all floats can be represented.
    
    irb(main):001>1.0.next_float
    =>1.0000000000000002

    irb(main):002>1.0.prev_float
    =>0.9999999999999999

### 8) Kernel#itself
Ruby went out and got itself an identity method. For those not familiar, an identity method returns the object it’s called on:
    
    irb(main):001>1.itself
    =>1

    irb(main):002>[2, 3, 3, 1, 2, 3, 3, 1, 1, 2].group_by(&:itself)
    =>{2=>[2, 2, 2], 3=>[3, 3, 3, 3], 1=>[1, 1, 1]}

### 8) Method#curry
You might not have realized that Ruby is capable of currying and partial application. In the past, you could only call `curry` on a `Proc`. This same power is now available to you on `Method`.
    
    def sum(*args)
      args.reduce(:+)
    end 
      
    irb(main):001>inc = method(:sum).curry(2).(1)
    =>#<Proc:0x007fff322d7420 (lambda)>
    irb(main):002>inc.(3)
    =>4

### 9) Method#super_method
Calling `super_method` returns the method that you would get if you called `super`. If the method has no parent, it returns `nil`.
    
    class Cat
      def speak
        'meow'
      end
    end

    class Tiger < Cat
      def speak
        'roar'
      end
    end
    
    irb(main):001>Tiger.new.method('speak')
    =>#<Method: Tiger#speak> 
    irb(main):002>Tiger.new.method('speak').super_method
    =>#<Method: Cat#speak>
    irb(main):002>Cat.new.method('speak').super_method
    =>nil

### 10) Quoted symbol keys in hashes with a trailing colon
Ruby 2.2 lets you create quoted symbol keys in hashes with a trailing colon:

    irb(main):001>{ 'programming-language': :ruby }


###References
https://github.com/ruby/ruby/blob/v2_2_0/NEWS

https://www.ruby-lang.org/en/news/2014/12/25/ruby-2-2-0-released/

http://www.sitepoint.com/new-methods-ruby-2-2/ 
    
