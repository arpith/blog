---
layout: post
date: 2015-11-28 16:31:20 GMT
tags:
- react native
- regular expressions
- regex
- apps
- ios
- performance
- dismiss keyboard
- word count
- faster
- code
- javascript
title: "Counting less"
---
<p></p><h2>Removing some expensive code</h2><p>A few days ago, I had written about <a href="http://arpith.co/post/134012448737/everybody-stand-back-i-know-regular-expressions">adding a word count</a>&nbsp;to <a href="http://github.com/constellational">Constellational</a>, my app for posting notes online. The function used a <a href="https://xkcd.com/208/">regular expression</a> to&nbsp;<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split">split the text you&rsquo;ve written</a> into an array, and <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter">filtered</a> out empty &ldquo;words&rdquo; before checking its length:</p><pre>     var wordCount = s.split(/\s+/).filter(w =&gt; (!!w)).length;</pre><p>Today I noticed that <a href="http://facebook.github.io/react-native/">React Native</a> was warning me that my code was too slow. Fortunately, I&rsquo;m only displaying this word count when the keyboard has been swiped down (because that&rsquo;s when I&rsquo;m in reading mode).</p><figure data-orig-width="540" data-orig-height="810" class="tmblr-full" data-orig-src="/images/d8e59f4b514340c6321ace53164afbbfd42f940b130d27f3b51de6e51dd4d3bf.png"><img src="/images/079da3dd51b9c76be9da04575a664db551de562d0f4d316f09f3765d03d7cb6f.png" data-orig-width="540" data-orig-height="810" data-orig-src="/images/d8e59f4b514340c6321ace53164afbbfd42f940b130d27f3b51de6e51dd4d3bf.png"></figure><p>I&rsquo;m going to <a href="https://github.com/constellational/iOS/issues/53">fix that center-alignment</a> :)</p><p>Anyway, this made speeding up <a href="https://github.com/constellational/iOS/blob/a2df7da1b4d94a43dad4e84105db97b89cb8e2a0/components/EditPage.js">the code</a> quite easy:</p><pre>   var countWords = () =&gt; {<br> &nbsp;    var words = this.state.post.data.split(/\s+/);<br> &nbsp;    return words.filter(w =&gt; !!w).length;<br>   };<br>   this.resetKeyboardSpace = () =&gt; {<br>     &nbsp;var newState = {<br> &nbsp; &nbsp;     height: this.state.fullHeight,<br> &nbsp; &nbsp;     isKeyboardUp: false,<br> &nbsp;     &nbsp; wordCount: countWords()<br> &nbsp;    };<br> &nbsp;   this.setState(newState);<br>   };</pre><p>I&rsquo;m using <a href="https://github.com/johanneslumpe/react-native-keyboardevents">react-native-keyboardevents</a> to call resetKeyboardSpace() when the keyboard is dismissed. I also decided not to display the word count when it&rsquo;s zero:</p><pre>    if (this.state.isKeyboardUp || !this.state.wordCount) {<br> &nbsp;    var title = &lt;Text&gt;&lt;/Text&gt;;<br>    } else {<br>     &nbsp;var title = &lt;Text style={styles.title}&gt;<br> &nbsp; &nbsp;    {this.state.wordCount} words<br>     &nbsp;&lt;/Text&gt;;<br>    }</pre><p>Thats about it! If you want a home for your notes online, <a href="http://eepurl.com/bHN6Mf">sign up here</a>!</p>