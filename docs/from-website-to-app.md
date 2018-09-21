---
layout: post
date: 2015-12-12 20:11:25 GMT
tags:
- writing
- reading
- sharing
- ios
- apps
- app development
- web
- web development
- notes
- constellational
- custom url
title: "From Website to App"
---
<h2>Opening your web content in your app</h2><p>You’ve <a href="http://arpith.co/post/133415247167/isomorphic-react-in-three-simple-steps">built</a> a <a href="http://arpith.co/post/134265953082/server-side-react-ejs">website</a>, and want to <a href="http://arpith.co/post/134800526522/deep-links-in-react-native">take a user to your app</a> if they’ve installed it. Basically, you want <a href="https://github.com/constellational/iOS/issues/28">universal links</a> but want to support iOS 7 and 8 as well.&nbsp;</p><figure class="tmblr-full" data-orig-height="1536" data-orig-width="2048"><img src="/images/58061c7316df15955481c469e09a6b1c84fe4eec4f4d53c88f3efcdfc973ee09.png" data-orig-height="1536" data-orig-width="2048"></figure><p>This is <a href="https://github.com/constellational/web/commit/b69d0f45c1032582326a10ab24d5b8b2389a1ee1">how I did it</a> in <a href="http://constellational.com/">Constellational</a>, the app <a href="http://github.com/constellational">I’m building</a> to&nbsp;<a href="http://arpith.co/post/134539082857/sharing-a-post">share</a> <a href="http://arpith.co/post/134069674682/drafts-unpublished-edits-and-posts">notes</a>.</p><pre>&lt;script type='text/javascript'&gt;<br> &nbsp;var url = window.location.href;<br> &nbsp;if (url.substring(0,7) === 'http://') {<br> &nbsp; &nbsp;url = url.substring(7);<br> &nbsp;} else if (url.substring(0,8) === 'https://') {<br> &nbsp; &nbsp;url = url.substring(8);<br> &nbsp;}<br> &nbsp;window.location.href = 'constellational://' + url;<br>&lt;/script&gt;</pre><p><a href="https://github.com/constellational/web/blob/b69d0f45c1032582326a10ab24d5b8b2389a1ee1/templates/layout.html">That code</a> checks if the <a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/location">url of the page</a> that the user is on looks like&nbsp;<a href="http://someurl/">http://constellational.com/someuser</a>&nbsp;or&nbsp;<a href="https://constellational.com/someuser,">https://constellational.com/someuser,</a> and replaces the protocol with my&nbsp;<a href="http://arpith.co/post/133201714402/using-a-link-to-sign-in-to-your-app">custom url scheme</a>, constellational://</p><p>Have a better way to do this? <a href="http://twitter.com/arpith">Let me know</a>!</p><p>Want to share notes? <a href="http://eepurl.com/bHN6Mf">Sign up here</a>!</p>