---
layout: post
date: 2015-12-08 17:41:34 GMT
tags:
- react native
- javascript
- ios
- apps
- ios app development
- universal links
- navigation
- reading
- web
- deep links
- url
- constellational
title: "Deep links in React Native"
---
<p>I’m <a href="http://github.com/constellational">building an app</a> to share notes (called <a href="http://constellational.com/">Constellational</a> — <a href="http://eepurl.com/bHN6Mf">sign up here</a>!), and I’m <a href="http://arpith.co/post/134737535752/keeping-track-of-what-youve-read">working</a> on <a href="http://arpith.co/post/134595293672/reading-everyone-you-love">opening user’s pages and posts in the app</a> instead of the&nbsp;<a href="http://arpith.co/post/134206819927/displaying-your-webpage">website</a>.&nbsp;</p><figure class="tmblr-full" data-orig-height="1536" data-orig-width="2048"><img src="/images/f3792e77101610a25533679d3eb6bbaabdd9c9e6e7084cc75abfe93c23f555e7.png" data-orig-height="1536" data-orig-width="2048"></figure><p><a href="https://github.com/constellational/iOS/blob/b1c2d7f49f7919be05441bf80ca7666752bd05b9/components/NavPage.js">This</a> is how you do it in <a href="https://facebook.github.io/react-native/">React Native</a>:</p><pre>var { LinkingIOS } = React;</pre><pre>class NavPage extends React.Component {<br> &nbsp;constructor(props, context) {<br> &nbsp; &nbsp;super(props, context);<br> &nbsp; &nbsp;var url = LinkingIOS.popInitialURL();<br> &nbsp; &nbsp;this.handleOpenURL = this.handleOpenURL.bind(this);<br> &nbsp; &nbsp;if (url) this.handleOpenURL({url});<br> &nbsp;}<br> &nbsp;<br> &nbsp;componentDidMount() {<br> &nbsp; &nbsp;LinkingIOS.addEventListener('url', this.handleOpenURL);<br> &nbsp;} </pre><pre> &nbsp;componentWillUnmount() {<br> &nbsp; &nbsp;LinkingIOS.removeEventListener('url', this.handleOpenURL);<br> &nbsp;}</pre><pre> &nbsp;handleOpenURL(event) {<br> &nbsp; &nbsp;var urlPath = event.url.split('constellational.com/')[1];<br> &nbsp; &nbsp;var splitPath = urlPath.split('/');<br> &nbsp; &nbsp;var username = urlPath.shift();<br> &nbsp; &nbsp;var postID = urlPath.shift();<br> &nbsp; &nbsp;this.props.navigator.push({<br> &nbsp; &nbsp; &nbsp;id: 'posts',<br> &nbsp; &nbsp; &nbsp;username: username,<br> &nbsp; &nbsp; &nbsp;postID: postID<br> &nbsp; &nbsp;});<br> &nbsp;}<br>}</pre><p><a href="https://github.com/constellational/iOS/blob/b1c2d7f49f7919be05441bf80ca7666752bd05b9/components/NavPage.js">What that does</a> is import <a href="https://facebook.github.io/react-native/docs/linkingios.html">LinkingIOS</a>, check if there’s a url (popInitialURL()), and set up a handler (handleOpenURL()) to take care of urls. The <a href="http://arpith.co/post/133415247167/isomorphic-react-in-three-simple-steps">urls</a>&nbsp;look like <a href="https://constellational.com/username/postID,">https://constellational.com/username/postID,</a> so the function first gets the username and postID before pushing a <a href="http://arpith.co/post/134409499417/two-steps-to-navigation-in-react-native">route to the navigator</a>.</p><p>That’s about it! <a href="https://github.com/constellational/iOS/issues/51">Another step</a> closer to <a href="https://github.com/constellational/iOS/issues/28">universal links</a>!</p>