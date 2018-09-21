---
layout: post
date: 2015-12-03 17:35:35 GMT
tags:
- ios
- react native
- apps
- ios app development
- navigation
- javascript
- constellational
- writing
- text editing
- notes
- first website
- code
title: "More Navigation in React Native"
---
# More Navigation in React Native

<h2>Using Navigator’s immediatelyResetRouteStack()</h2><p>I’m <a href="http://github.com/constellational">building an app</a> (<a href="http://constellational.com">Constellational</a>) to post notes online, and one thing I’ve wanted is to go to the <a href="http://arpith.co/post/133875065157/react-native-full-screen-textinputs">Edit Page</a> after you <a href="http://arpith.co/post/133201714402/using-a-link-to-sign-in-to-your-app">sign in </a>— this is so that you can get straight down to the business of writing a note.&nbsp;</p><figure class="tmblr-full" data-orig-height="600" data-orig-width="800"><img src="/images/d2d75f3d10c3406548c5f541f6451d90c03b4872f9f408e94a6e73b5db342a9b.jpg" data-orig-height="600" data-orig-width="800"></figure><p><a href="https://github.com/constellational/iOS/blob/2280cb123b1cfbce6f0a519d485c689bb4d94c76/components/WelcomePage.js">To do this</a>, I’m using <a href="https://facebook.github.io/react-native/">React Native</a>’s <a href="https://facebook.github.io/react-native/docs/navigator.html">immediatelyResetRouteStack()</a>:</p><pre>getStarted() {<br> &nbsp;this.props.navigator.immediatelyResetRouteStack([<br> &nbsp; &nbsp;{id: 'navigation'},<br> &nbsp; &nbsp;{id: 'posts'},<br> &nbsp; &nbsp;{id: 'edit'}<br> &nbsp;]);<br>}</pre><p>This <a href="https://facebook.github.io/react-native/docs/javascript-environment.html#content">class method</a> gets called once a <a href="http://arpith.co/post/133201714402/using-a-link-to-sign-in-to-your-app">user is signed in</a> (and taps the ‘Get Started’ button). What it does is pass an array of routes — in our case, objects with ids will do — to the <a href="http://arpith.co/post/134409499417/two-steps-to-navigation-in-react-native">Navigator</a>’s immediatelyResetRouteStack() method. </p><p>This means that once you’re signed in you will end up in the Edit Page where the cancel button (which pops on the Navigator) will take you to the Posts Page, whose back button will then take you to the Navigation Page. That’s as simple as it gets!</p><p>Sounds interesting? Want a home for your notes online? <a href="http://eepurl.com/bHN6Mf">Sign up here</a>.</p>