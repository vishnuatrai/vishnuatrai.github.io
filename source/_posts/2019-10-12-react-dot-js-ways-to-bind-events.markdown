---
layout: post
title: "React.js - Ways to Bind Events"
date: 2019-10-12 11:47:20 +0530
comments: true
description: React.js - Ways to Bind Events
keywords: React.js, ReactJs, Event Bindings, Bind Events
categories:
  React.js
  ReactJs
  Event&nbsp;Bindings
  Binding&nbsp;Events
  Front&nbsp;End&nbsp;Design&nbsp;Patterns
  Front&nbsp;End
---


###Background
Event binding in ReactJs components required because of `this` keyword works in JavaScript, within click handler function `this` keyword will lost its context (component instance) or value. 

###1. `bind` the handler in JSX render<!--more-->

	class BindHandler extends React.Component {
			constructor(props){
				...
			}
			handlerFunction() { 
				console.log(this)
			}
			render(){
				return(<button onClick={this.handlerFunction.bind(this)}>Click Event</button>)
			}
		}

###2. Arrow function in JSX render callback(`this` is bound lexically)

	class ArrowFunctionBinding extends React.Component {
			constructor(props){
				...
			}
			handlerFunction() { 
				console.log(this)
			}
			render(){
				return(<button onClick={() => this.handlerFunction()}>Click Event</button>)
			}
		}

###3. `bind` in `constructor()`

	class ConstructorBinding extends React.Component {
			constructor(props){
				...
				this.handlerFunction = this.handlerFunction.bind(this)
			}
			handlerFunction() { 
				console.log(this)
			}
			render(){
				return(<button onClick={this.handlerFunction}>Click Event</button>)
			}
		}

###4. Define handler function as class property using Arrow function

	class EventHandlerClassProperty extends React.Component {
			constructor(props){
				...
				this.handlerFunction = this.handlerFunction.bind(this)
			}
			handlerFunction = () => { 
				console.log(this)
			}
			render(){
				return(<button onClick={this.handlerFunction}>Click Event</button>)
			}
		}

###Recommendations

React documentation suggests binding events in constructor. But if need to use **arrow function in JSX render callback** for its simplicity or when need to pass arguments, we should consider caching the handlers if the bindings become a performance issue.

