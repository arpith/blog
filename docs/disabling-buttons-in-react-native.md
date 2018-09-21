---
layout: post
date: 2015-12-15 21:31:22 GMT
tags:
- ui
- ux
- design
- apps
- ios
- ios app development
- react native
- javascript
- notes
- reading
- writing
- sharing
- constellational
- posts
- update
- drafts
title: "Disabling Buttons in React Native"
---
# Disabling Buttons in React Native

<p>I’m <a href="http://github.com/constellational">building an app</a> to <a href="http://arpith.co/post/134539082857/sharing-a-post">share notes</a> called <a href="http://constellational.com/">Constellational</a> (<a href="http://eepurl.com/bHN6Mf">sign up here!</a>) and<a href="https://github.com/constellational/iOS/issues/66">I’ve wanted</a> to prevent blank posts and “updates” that don’t really have changes. To do this, I’ve disabled the <a href="http://arpith.co/post/134069674682/drafts-unpublished-edits-and-posts">Post and Update buttons</a> unless there is a change from the initial state (this will be a blank string if we’re creating a new post).<br></p><p><br></p><figure class="tmblr-full" data-orig-height="1536" data-orig-width="2048"><img src="/images/9417b32988928832c13f5e8c97d353be6fd987fa73c1c24f57c02d1d6e123071.png" data-orig-height="1536" data-orig-width="2048"></figure><p><br></p><p><a href="https://github.com/constellational/iOS/commit/a7555bc75c59a99a879465780d8ccac7eafb3a15">This is how</a> I did it in <a href="https://facebook.github.io/react-native/">React Native</a>. In the constructor for my <a href="https://github.com/constellational/iOS/blob/a7555bc75c59a99a879465780d8ccac7eafb3a15/components/EditPage.js">EditPage component</a>:</p><pre>this.state.shouldDisablePostButton = true;<br>if (this.state.post.isDraft) {<br> &nbsp;this.state.shouldDisablePostButton = false;<br>}</pre><p>Note that if it’s a <a href="http://arpith.co/post/134069674682/drafts-unpublished-edits-and-posts">draft</a> we’re editing, we’re never going to want to disable the Post button.</p><p>In the <a href="https://github.com/constellational/iOS/blob/a7555bc75c59a99a879465780d8ccac7eafb3a15/components/EditPage.js">EditPage component</a>’s render method:</p><pre>var postButton = &lt;PostButton <br> &nbsp;edit={this.isEditing}<br> &nbsp;isDraft={this.state.post.isDraft}<br> &nbsp;onPress={this.savePost}<br> &nbsp;disabled={this.state.shouldDisablePostButton}<br>/&gt;;</pre><p>The onChangeText method for the <a href="https://facebook.github.io/react-native/docs/textinput.html">TextInput</a> also needed to be changed:</p><pre>onChangeText={(text) =&gt; {<br> &nbsp;this.state.post.data = text;<br> &nbsp;this.setState({<br> &nbsp; &nbsp;shouldDisablePostButton: !this.state.post.isDraft &amp;&amp; (this.state.post.data === this.initialData)<br> &nbsp;});<br>}}</pre><p>The <a href="https://github.com/constellational/iOS/blob/a7555bc75c59a99a879465780d8ccac7eafb3a15/components/PostButton.js">PostButton component</a> uses the <a href="https://github.com/ide/react-native-button">React Native Buttons package</a>:</p><pre>var Button = require('react-native-button');<br>class PostButton extends React.Component {<br> &nbsp;render() {<br> &nbsp; &nbsp;var text = 'Post';<br> &nbsp; &nbsp;if (!this.props.isDraft &amp;&amp; this.props.edit) text = 'Update';<br> &nbsp; &nbsp;return &lt;Button<br> &nbsp; &nbsp; &nbsp;onPress={this.props.onPress}<br> &nbsp; &nbsp; &nbsp;style={styles.postButton}<br> &nbsp; &nbsp; &nbsp;styleDisabled={styles.disabled}<br> &nbsp; &nbsp; &nbsp;disabled={this.props.disabled}<br> &nbsp; &nbsp;&gt;<br> &nbsp; &nbsp; &nbsp;{text}<br> &nbsp; &nbsp;&lt;/Button&gt;;<br> &nbsp;}<br>}</pre><p>And that’s it! This fixes <a href="https://github.com/constellational/iOS/issues/66">#66.</a></p>