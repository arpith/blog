---
layout: post
date: 2015-12-14 17:28:13 GMT
tags:
- typography
- css
- web development
- apps
- ios
- sharing
- writing
- reading
- constellational
title: "San Francisco in CSS"
---
<p>I’m <a href="http://github.com/constellational">building an app</a> to share notes, <a href="http://constellational.com/">Constellational</a>, and the <a href="http://arpith.co/post/133803823587/san-francisco-in-react-native">font it uses</a> is the Apple system font — which is San Francisco in iOS9. I want the website to reflect the experience of using the app as far as possible, so <a href="https://github.com/constellational/web/commit/cd7d74f214cd5b0ddabffe73bc1a4e1cb28306c9">here’s how I used San Francisco on the web</a>:</p><pre>body {<br> &nbsp;font-family: '-apple-system', 'Helvetica Neue', helvetica , sans!important;<br> &nbsp;text-rendering: optimizeLegibility!important;<br>}</pre><p>That’s it! This is thanks to <a href="http://stackoverflow.com/questions/32660748/how-to-use-apples-new-san-francisco-font-on-a-webpage">this StackOverflow answer</a> and <a href="http://furbo.org/2015/07/09/i-left-my-system-fonts-in-san-francisco/">this blog post</a>.</p><p>Sound interesting? <a href="http://eepurl.com/bHN6Mf">Sign up here</a>!</p>