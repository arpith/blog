---
layout: post
date: 2015-12-22 14:37:15 GMT
tags:
- reactjs
- react-router
- css
- animation
- client side
- web development
- web app
- transition
- opacity
- javascript
title: "Animating React Components"
---
<p>A few days ago I talked about <a href="http://arpith.co/post/135499760202/server-rendering-with-react-router">server rendering</a>&nbsp;with <a href="https://github.com/rackt/react-router">React Router</a>, and yesterday I talked about <a href="http://arpith.co/post/135643412857/using-react-router">using it on the client side</a> to render components based on url path, for example, rendering the home page when the url is ‘/’ and the about page when the url is ‘/about’.</p><p>Today’s post is about how you can <a href="https://github.com/rackt/react-router/blob/master/examples/animations/app.js">animate these changes</a>.</p><figure data-orig-width="1200" data-orig-height="900" class="tmblr-full"><img src="/images/bb66a6e9631a70fb6ac50eb5664d182f266d871e2b27eb352a993b2b845f3c31.jpg" data-orig-width="1200" data-orig-height="900"></figure><h2>Use ReactCSSTransitionGroup</h2><p>Wrap the components you want to animate in <a href="https://facebook.github.io/react/docs/animation.html">ReactCSSTransitionGroup</a>. <a href="http://arpith.co/post/135643412857/using-react-router">Yesterday’s</a> app component now looks like:</p><pre>import React from 'react';<br>import ReactCSSTransitionGroup from 'react-addons-css-transition-group';<br>import Header from './Header';<br> &nbsp;<br>class App extends React.Component {<br> &nbsp;render() {<br> &nbsp; &nbsp;return (<br> &nbsp; &nbsp; &nbsp;&lt;div&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp;&lt;Header /&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp;&lt;ReactCSSTransitionGroup<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;transitionName="example"<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;transitionEnterTimeout={500}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;transitionLeaveTimeout={300}<br> &nbsp; &nbsp; &nbsp; &nbsp;&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{React.cloneElement(this.props.children, {key: page})}<br> &nbsp; &nbsp; &nbsp; &nbsp;&lt;/ReactCSSTransitionGroup&gt;<br> &nbsp; &nbsp; &nbsp;&lt;/div&gt;<br> &nbsp; &nbsp;);<br> &nbsp;}<br>}</pre><pre>export default App;</pre><p><a href="https://facebook.github.io/react/docs/clone-with-props.html">React’s cloneElement()</a> lets you add a key to the child component passed by React Router — this is how React determines which children have entered, left or stayed.</p><h2>Add CSS Classes</h2><p>There are a <a href="http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/">bunch of things</a> you can animate with CSS, these classes use opacity to produce a fade-in effect:</p><pre>.example-enter {<br> &nbsp;opacity: 0.01;<br>}</pre><pre>.example-enter.example-enter-active {<br> &nbsp;opacity: 1;<br> &nbsp;transition: opacity 500ms ease-in;<br>}</pre><pre>.example-leave {<br> &nbsp;opacity: 1;<br>}</pre><pre>.example-leave.example-leave-active {<br> &nbsp;opacity: 0.01;<br> &nbsp;transition: opacity 300ms ease-in;<br>}</pre><p>That’s about it!</p><p>Doing something cool with React? <a href="http://twitter.com/arpith">Let me know</a>!</p>