---
layout: post
date: 2015-12-16 19:28:56 GMT
tags:
- constellational
- react native
- ios
- app
- ios app development
- reading
- writing
- sharing
- history
- ui
- ux
- flux
- notes
- sort
- javascript
title: "Remembering what you read last"
---
<p>The app <a href="http://github.com/constellational">I’m building</a> to <a href="http://arpith.co/post/134539082857/sharing-a-post">share notes</a>, <a href="http://constellational.com/">Constellational</a>, helps you <a href="http://arpith.co/post/134737535752/keeping-track-of-what-youve-read">keep track of what you’ve read recently</a>. To let you re-read these <a href="http://arpith.co/post/134595293672/reading-everyone-you-love">posts or user pages</a>, the navigation page displays your history as a list. It makes sense to show the last visit alone (this also keeps the list as brief as possible).</p><figure class="tmblr-full" data-orig-height="600" data-orig-width="800"><img src="/images/40b87e6ceee47d00a28f61527871df2b73c8734dfa5149f34ad0d5b72a1f31b9.jpg" data-orig-height="600" data-orig-width="800"></figure><p><a href="https://github.com/constellational/iOS/commit/94b986f769b68512ee34594ac0277d52df3a700d">This is how I did it</a> in <a href="https://facebook.github.io/react-native/">React Native</a>.</p><p>When a user or a particular post is viewed in the <a href="https://github.com/constellational/iOS/blob/9fa20dbb5f7dd7ef0ffbea45adb61004c6d9ef9d/components/PostsPage.js">posts page</a>, the url (username/postID, or just username) is added to the HistoryStore:</p><pre>constructor(props, context) {<br> &nbsp;super(props, context);<br> &nbsp;if (this.props.username) {<br> &nbsp; &nbsp;HistoryActions.add({<br> &nbsp; &nbsp; &nbsp;username: this.props.username,<br> &nbsp; &nbsp; &nbsp;postURL: this.props.postURL,<br> &nbsp; &nbsp; &nbsp;url: this.props.url<br> &nbsp; &nbsp;});<br> &nbsp;}<br>}</pre><p>The <a href="https://github.com/constellational/iOS/blob/9fa20dbb5f7dd7ef0ffbea45adb61004c6d9ef9d/stores/HistoryStore.js">HistoryStore</a> keeps track of visits that look like {url: url, timestamps: [secondVisit, firstVisit]}</p><pre>var HistoryStore = assign({}, EventEmitter.prototype, {<br> &nbsp;get: function() {<br> &nbsp; &nbsp;if (!_history) {<br> &nbsp; &nbsp; &nbsp;loadAsyncStore();<br> &nbsp; &nbsp; &nbsp;return [];<br> &nbsp; &nbsp;} else {<br> &nbsp; &nbsp; &nbsp;var history = Object.keys(_history).map(url =&gt; _history[url]);<br> &nbsp; &nbsp; &nbsp;var sorted = history.sort((a, b) =&gt; {<br> &nbsp; &nbsp; &nbsp; &nbsp;if (a.timestamps[0] &gt; b.timestamps[0]) return -1;<br> &nbsp; &nbsp; &nbsp; &nbsp;else if (a.timestamps[0] &lt; b.timestamps[0]) return 1;<br> &nbsp; &nbsp; &nbsp; &nbsp;else return 0;<br> &nbsp; &nbsp; &nbsp;});<br> &nbsp; &nbsp; &nbsp;return sorted;<br> &nbsp; &nbsp;}<br> &nbsp;}<br>});</pre><p>Note that the get() method sorts the visits based on the most recent timestamp (the first one in the timestamps list for each visit).</p><p>This lets the <a href="https://github.com/constellational/iOS/blob/9fa20dbb5f7dd7ef0ffbea45adb61004c6d9ef9d/components/NavPage.js">NavPage</a> display a list of the users and posts you’ve read, sorted by when you read them (without duplicates).</p><pre>renderRow(row) {<br> &nbsp;let route = {<br> &nbsp; &nbsp;id: 'posts',<br> &nbsp; &nbsp;username: row.username,<br> &nbsp; &nbsp;postURL: row.url,<br> &nbsp; &nbsp;url: row.username<br> &nbsp; };<br> &nbsp; if (row.id) route.url = row.username + '/' + row.id;<br> &nbsp; let onPress = () =&gt; this.props.navigator.push(route);<br> &nbsp; let text = row.username;<br> &nbsp; if (row.data) text = row.data.split('\n')[0];<br> &nbsp; return &lt;View style={styles.option}&gt;<br> &nbsp; &nbsp; &lt;Text onPress={onPress} style={styles.text}&gt;{text}&lt;/Text<br> &nbsp; &lt;/View&gt;;<br> }</pre><p>This fixes <a href="https://github.com/constellational/iOS/issues/68">#68</a>.</p><p>Do you want a pleasant way to share notes? <a href="http://eepurl.com/bHN6Mf">Sign up here!</a></p>