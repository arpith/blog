---
layout: post
date: 2015-12-04 18:58:08 GMT
tags:
- react native
- ios
- apps
- ios app development
- sharing
- app extensions
- publishing
- posts
- constellational
- javascript
title: "Sharing a post"
---
<p><a href="http://constellational.com/">Constellational</a> (the <a href="http://github.com/constellational">app I’m building</a> to give your notes a home online) lets you share posts via the usual suspects – email, message, Twitter, Facebook, etc. I’m doing this using React Native’s <a href="https://facebook.github.io/react-native/docs/actionsheetios.html">ActionSheetIOS</a>.</p><figure class="tmblr-full" data-orig-height="600" data-orig-width="800"><img src="/images/58181be56fe980b2d08c6a59c611a7c783613bc978bacaa3cd2e06480c3f3a67.jpg" data-orig-height="600" data-orig-width="800"></figure><p>When a post is tapped a bunch of options show up (Share, Edit, Delete or Cancel), and if Share has been tapped (buttonIndex is zero) the Share sheet is displayed.<br></p><p><a href="https://github.com/constellational/iOS/blob/b14e7ad8a89fc120e846ab08c90f1bf7064c104b/components/Post.js">This is the code</a> that we’re interested in:</p><pre>showOptions() {<br> &nbsp;ActionSheetIOS.showActionSheetWithOptions({<br> &nbsp; &nbsp;options: ['Share', 'Edit', 'Delete', 'Cancel'],<br> &nbsp; &nbsp;destructiveButtonIndex: 2,<br> &nbsp; &nbsp;cancelButtonIndex: 3<br> &nbsp;}, (buttonIndex) =&gt; {<br> &nbsp; &nbsp;var url = URL + '/' + SettingStore.getUsername() + '/' + this.props.post.id;<br> &nbsp; &nbsp;if (this.props.post.isDraft || this.props.post.hasUnpublishedEdits) {<br> &nbsp; &nbsp; &nbsp;var options = {message: this.props.post.data};<br> &nbsp; &nbsp;} else {<br> &nbsp; &nbsp; &nbsp;var options = {url: url};<br> &nbsp; &nbsp;}<br> &nbsp; &nbsp;if (buttonIndex === 0) {<br> &nbsp; &nbsp; &nbsp;ActionSheetIOS.showShareActionSheetWithOptions(options, this.shareFailure, this.shareSuccess);<br> &nbsp; &nbsp;} else if (buttonIndex === 1) {<br> &nbsp; &nbsp; &nbsp;this.props.nav.push({id: 'edit', post: this.props.post});<br> &nbsp; &nbsp;} else if (buttonIndex === 2) {<br> &nbsp; &nbsp; &nbsp;if (this.props.post.isDraft) {<br> &nbsp; &nbsp; &nbsp; &nbsp;DraftActions.del(this.props.post);<br> &nbsp; &nbsp; &nbsp;} else {<br> &nbsp; &nbsp; &nbsp; &nbsp;PostActions.del(this.props.post);<br> &nbsp; &nbsp; &nbsp;} <br> &nbsp; &nbsp;}<br> &nbsp;});<br>}</pre><p>Note that if the post is being edited or is still a draft the post contents are shared (for example, if you chose email the body of the mail will be the text of the post). A link is shared if you’ve published the post. Still to come:<a href="https://github.com/constellational/iOS/issues/51">opening these links in the app instead of the browser</a>. Sounds interesting?<a href="http://eepurl.com/bHN6Mf">Sign up here!</a></p>