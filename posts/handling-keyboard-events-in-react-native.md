---
layout: post
date: 2015-12-09 19:15:21 GMT
tags:
- ios
- app
- ios app development
- react native
- javascript
- keyboard
- writing
- publishing
- sharing
- ui
- full screen
- notes
- website
- npm
- constellational
title: "Handling Keyboard Events in React Native"
---
# Handling Keyboard Events in React Native

<p>I’m <a href="https://github.com/constellational">building an app</a> called <a href="http://constellational.com/">Constellational</a> to share notes (<a href="http://eepurl.com/bHN6Mf">sign up here</a>!) and a big part of this is a <a href="http://arpith.co/post/133875065157/react-native-full-screen-textinputs">full screen</a>, <a href="http://arpith.co/post/133803823587/san-francisco-in-react-native">distraction-free</a> <a href="https://facebook.github.io/react-native/docs/textinput.html">Text Input</a>.</p><figure data-orig-width="2048" data-orig-height="1536" class="tmblr-full"><img src="/images/78a1d8ebcc95a346ea974e709ab7af5292689108b76c59f909a5060c4381dba1.png" data-orig-width="2048" data-orig-height="1536"></figure><p><a href="http://arpith.co/post/134130800147/counting-less">I was</a> <a href="http://arpith.co/post/134012448737/everybody-stand-back-i-know-regular-expressions">using</a> an <a href="https://www.npmjs.com/">NPM</a> package called <a href="https://github.com/johanneslumpe/react-native-keyboardevents">React Native Keyboard Events</a> to do this, but it <a href="https://github.com/facebook/react-native/issues/2819">looks like</a> that is <a href="http://ollie.relph.me/blog/react-native-0-11-keyboard-display-events/">no longer necessary</a>.</p><pre>{ DeviceEventEmitter } = React;</pre><pre>class EditPage extends React.Component {<br> &nbsp;constructor(props, context) {<br> &nbsp; &nbsp;super(props, context);<br> &nbsp; &nbsp;this.updateKeyboardSpace = (deviceEvent) =&gt; {<br> &nbsp; &nbsp; &nbsp;var change = deviceEvent.endCoordinates.height;<br> &nbsp; &nbsp; &nbsp;this.setState({<br> &nbsp; &nbsp; &nbsp; &nbsp;height: this.state.height - change,<br> &nbsp; &nbsp; &nbsp; &nbsp;isKeyboardUp: true<br> &nbsp; &nbsp; &nbsp;});<br> &nbsp; &nbsp;};<br> &nbsp; &nbsp;this.resetKeyboardSpace = () =&gt; this.setState({<br> &nbsp; &nbsp; &nbsp;height: this.state.fullHeight,<br> &nbsp; &nbsp; &nbsp;isKeyboardUp: false<br> &nbsp; &nbsp;});<br> &nbsp;}</pre><pre> &nbsp;componentDidMount() {<br> &nbsp; &nbsp;DeviceEventEmitter.addListener('keyboardWillShow', this.updateKeyboardSpace);<br> &nbsp; &nbsp;DeviceEventEmitter.addListener('keyboardWillHide', this.resetKeyboardSpace);<br> &nbsp;}<br> &nbsp;<br> &nbsp;render() {<br> &nbsp; &nbsp;return (<br> &nbsp; &nbsp; &nbsp;&lt;View style={styles.page} onLayout={(ev) =&gt; {<br> &nbsp; &nbsp; &nbsp; &nbsp;// 80 is for the navbar on top<br> &nbsp; &nbsp; &nbsp; &nbsp;var fullHeight = ev.nativeEvent.layout.height - 80;<br> &nbsp; &nbsp; &nbsp; &nbsp;this.setState({height: fullHeight, fullHeight: fullHeight});<br> &nbsp; &nbsp; &nbsp;}}&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp;&lt;NavBar leftButton={this.cancelButton} title={title} rightButton={this.postButton}/&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp;&lt;ScrollView keyboardDismissMode='interactive'&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&lt;TextInput<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;multiline={true}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;onChangeText={(text) =&gt; {<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;this.state.post.data = text;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;}}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;defaultValue={this.state.post.data}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;autoFocus={true}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;style={[styles.input, {height:this.state.height}]}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;/&gt;<br> &nbsp; &nbsp; &nbsp; &nbsp;&lt;/ScrollView&gt;<br> &nbsp; &nbsp; &nbsp;&lt;/View&gt;<br> &nbsp; &nbsp;);<br> &nbsp;}<br>}</pre><p>What’s going on <a href="https://github.com/constellational/iOS/blob/0143ced94fa9cff34268dc48c6776e656bdf1490/components/EditPage.js">here</a>: when the component mounts, a listener is added to the ‘keyboardWillShow’ (and hide) event. This listener adjusts the height of the TextInput to match the size of the visible screen (that’s the height of the screen minus the height of the keyboard). That’s about it!</p><p>Bonus: should I <a href="https://github.com/constellational/iOS/issues/60">use Dimensions.get(‘window’).height</a>?</p>