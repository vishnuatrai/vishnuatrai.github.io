---
layout: post
title: "React.js - HOCs vs Render Props vs Custom Hooks Patterns"
date: 2020-02-02 11:57:42 +0530
comments: true
description: React.js - HOCs vs Render Props vs Custom Hooks Patterns
keywords: React.js, ReactJs, HOCs, Render Props, Custom Hooks, FrontEnd, Design Patterns
categories:
  React.js
  ReactJs
  HOC
  Render&nbsp;Props
  Custom&nbsp;Hooks
  Front&nbsp;End&nbsp;Design&nbsp;Patterns
  Front&nbsp;End
---

Higher-Order Components(HOCs), Render Props and Custom Hooks are techniques to share comman functionality between components. It's recommended to always go with hooks wherever possible. HOCs and render props patterns require you to restructure your components when you use them, which can be cumbersome and make code harder to follow.
###Higher-Order Components
Higher-order component is a function that takes a component as an argument and returns a new component. HOC adds additional data and functionality to original component so new component also refered to as Enhanced component.<!--more-->
	const NewComponent = higherOrderComponent(OriginalComponent)
	const EnhancedComponent = higherOrderComponent(WrappedComponent)
Other possibilties with HOCs,<br />
1. Passing down the props using spread operator <br />
2. Passing parameters to higher order functions
	#example 
	const withAdditionalFunctionality = (WrappedComponent, additionalArgs) => {
		class WithAdditionalFunctionality extends React.Component {
			constructor(props){
				super(props)
				this.state = { 
					state1: 'value1'
					... 
				}
			}
			additionalFunctionality = () => { 
				//additionalArgs can be use here 
				... 
			}
			render(){
				return( <WrappedComponent
							state1={this.state.state1} 
							additionalFunctionality={this.additionalFunctionality} 
							{...this.props} 
						/> )
			}
		}
		return WithAdditionalFunctionality;
	}
	export default withAdditionalFunctionality;

###Render Props Pattern
Render Props is another pattern for sharing code between React components using a prop whose value is a function. 
	class RenderProps extends React.Component {
			constructor(props){
				super(props)
				this.state = { 
					state1: 'value1'
					... 
				}
			}
			additionalFunctionality = () => { 
				//props.additionalArgs
				... 
			}
			render(){
				return(<div>
						{ this.props.render( state1={this.state.state1}
							additionalFunctionality={this.additionalFunctionality} ) }
					</div>)
			}
		}
		export default RenderProps;
		#Usage
		<RenderProps 
			render={ (state1, additionalFunctionality) => { return <div> .... </div> } } 
		/>
		<RenderProps 
			render={ (state1, additionalFunctionality) => { return <p> .... </p> } } 
		/>
###Custom Hooks
Simpler alternative to HOCs and Render Props to share the common logic between components. A custom hook can also call other hooks if required. With Hooks, you can extract stateful logic from a component so it can be tested independently and reused. Hooks allow you to reuse stateful logic without changing your component hierarchy. 
	import {useState} from 'react'
	function useCustomHook(arguments){
		const [state1, setState1] = useState(arguments.initialState)
		const additionalFunctionality = () => {
		   //arguments can be used here
		   .....
		}
		....
		return [state1, additionalFunctionality];
	}
	export default useCustomHook;
	#Usage
	function ConsumerComponent {
		const [state1, additionalFunctionality] = useCustomHook(initialValues and args...)
		render(
			<div>
				....
			</div>
		)
	}
