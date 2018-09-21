---
layout: post
date: 2015-12-11 17:06:28 GMT
tags:
- email
- sharing
- posting
- writing
- reading
- subjects
- apps
- ios
- react native
- javascript
- ios app development
- notes
- constellational
title: "Email subjects — why bother?"
---
# Email subjects — why bother?

<p>A few days ago I explained how <a href="http://constellational.com/">Constellational</a>, the <a href="http://github.com/constellational">app I’m building</a> to sharing notes, lets you<a href="https://medium.com/@arpith/sharing-a-post-520659ff3857#.ukwb7qdvp"> </a><a href="http://arpith.co/post/134539082857/sharing-a-post">share your posts</a> via email if it’s a <a href="http://arpith.co/post/134069674682/drafts-unpublished-edits-and-posts">draft or an edit you’re working on</a>. Today I found out that React Native now <a href="https://facebook.github.io/react-native/docs/actionsheetios.html">supports subject lines when sharing through email</a>.</p><p>This is something <a href="https://github.com/constellational/iOS/issues/39">I’ve wanted</a> for a while, because an email without a subject is an email that’s not going to get read. You know how many emails are lying unopened in your inbox.</p><figure class="tmblr-full" data-orig-height="1536" data-orig-width="2048"><img src="/images/7b08f3f820f1eb23ff1b2ada8a1996f0d16133187c92187a235c2d66989faaef.png" data-orig-height="1536" data-orig-width="2048"></figure><p>The <a href="https://github.com/constellational/iOS/commit/84aacc2f83169cdac110a34490c04828b7d55b44">change I had to make</a> to my app’s <a href="https://github.com/constellational/iOS/blob/84aacc2f83169cdac110a34490c04828b7d55b44/components/Post.js">code</a> was tiny:</p><pre>var username = SettingStore.getUsername();<br>var post = this.props.post;<br>var url = URL + '/' + username + '/' + post.id;<br>var options = {subject: post.data.split('\n')[0]};<br>if (post.isDraft || post.hasUnpublishedEdits) {<br> &nbsp;options.message = post.data;<br>} else {<br> &nbsp;options.url = url;<br>}<br>ActionSheetIOS.showShareActionSheetWithOptions(options, this.shareFailure, this.shareSuccess);</pre><p>Note that I’m taking the first line of the post as the email’s subject, perhaps this should be trimmed. This fixes <a href="https://github.com/constellational/iOS/issues/39">#39</a>.</p><p>Want to share your notes? <a href="http://eepurl.com/bHN6Mf">Sign up here</a>!</p>