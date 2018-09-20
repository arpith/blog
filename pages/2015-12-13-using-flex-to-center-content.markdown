---
layout: post
date: 2015-12-13 18:28:27 GMT
tags:
- constellational
- react native
- app
- ios
- flex
- ui
- ux
- navigation
- word count
- writing
- sharing
- publishing
- reading
title: "Using flex to center content"
---
<p>I’m <a href="http://github.com/constellational">building an app</a> to <a href="http://arpith.co/post/134539082857/sharing-a-post">share notes</a> called <a href="http://constellational.com/">Constellational</a>, and a <a href="http://arpith.co/post/133875065157/react-native-full-screen-textinputs">big part of it</a>&nbsp;is creating a <a href="http://arpith.co/post/133803823587/san-francisco-in-react-native">pleasant environment</a> to read and write in. I’ve found that keeping track of the post length helps me write better, so when you <a href="http://arpith.co/post/134869397397/handling-keyboard-events-in-react-native">swipe the keyboard down</a> to read your post <a href="http://arpith.co/post/134012448737/everybody-stand-back-i-know-regular-expressions">a word count shows up</a> in the&nbsp;<a href="http://arpith.co/post/134130800147/counting-less">navigation bar</a>.<br></p><figure data-orig-width="640" data-orig-height="859" class="tmblr-full"><img src="/images/09630b3999394d4e3dee5221c4b58deb1f6b247bee03697de4a6d34c29bedc46.jpg" data-orig-width="640" data-orig-height="859"></figure><p>The navigation bar has a button on the left (<a href="http://arpith.co/post/134409499417/two-steps-to-navigation-in-react-native">to go back</a>), a title in the middle (the word count) and also says <a href="http://arpith.co/post/134069674682/drafts-unpublished-edits-and-posts">Post (or Update)</a> on the right. Since the left and right buttons are of unequal sizes, this meant the word count wasn’t centered. Fixing this using <a href="https://developer.mozilla.org/en/docs/Web/CSS/flex">flex</a> is straightforward:<br></p><pre>var styles = StyleSheet.create({
  navBar: {
    flexDirection: ‘row’
  },
  left: {
&nbsp; &nbsp;flex: 1
 &nbsp;},
 &nbsp;right: {
 &nbsp; &nbsp;flex: 1,
 &nbsp; &nbsp;alignItems: 'flex-end'
 &nbsp;},
 &nbsp;title: {
 &nbsp; &nbsp;textAlign: 'center'
 &nbsp;}
});</pre><p><a href="https://github.com/constellational/iOS/blob/fca5b1a13dff188729d1d95aa04a4c2f6c1e7d88/components/NavBar.js">This</a> sets the sizes of the left and right buttons to be equal, which in turn makes the word count stay centered (with a little help from textAlign). Note that this fixes <a href="https://github.com/constellational/iOS/issues/53">#53</a>.</p>