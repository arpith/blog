---
layout: post
date: 2015-11-24 18:42:55 GMT
tags:
- react native
- ios
- text input
- writing
- apps
- full screen text editing
- distraction free
- dismiss keyboard
- ui
title: "React Native: Full Screen TextInputs"
---
# React Native: Full Screen TextInputs

The [app I’m building](http://github.com/constellational/iOS), [Constellational](http://github.com/constellational), lets you [post your notes online](http://arpith.co/post/133803823587/san-francisco-in-react-native) — and a key element is a full screen, distraction-free zone for you to type your notes. [This is the code](https://github.com/constellational/iOS/blob/a0147ffdf7e1498fedf19764d1a5106aaa43fcdd/components/EditPage.js) we’re interested in:

    render() {
      return (
        <view style="{styles.page}" onlayout="{(ev)"> {
          // 80 is for the navbar on top
          var fullHeight = ev.nativeEvent.layout.height — 80;
          this.setState({height: fullHeight, fullHeight: fullHeight});
        }}>
          <navbar leftbutton="{this.cancelButton}" rightbutton="{this.postButton}/"><scrollview keyboarddismissmode="interactive"><textinput multiline="{true}" onchangetext="{(text)"> {
                this.state.post.data = text;
              }}
              defaultValue={this.state.post.data}
              autoFocus={true}
              style={[styles.input, {height:this.state.height}]}
            />
          </textinput></scrollview></navbar></view>
      );
    }}
    
There are two things that are interesting here, that you can use the [onLayout property](https://facebook.github.io/react-native/docs/view.html#onlayout) of a [React Native](https://facebook.github.io/react-native/) [View](https://facebook.github.io/react-native/docs/view.html#content) to set the height of the [TextInput](https://facebook.github.io/react-native/docs/textinput.html#content) (we want it to occupy all the visible space whether the keyboard is up or dismissed).

The other interesting thing is being able to [dismiss the keyboard](https://github.com/constellational/iOS/issues/47) when scrolling through a longer post that you’re writing. [The way that seems to work best](http://stackoverflow.com/questions/29685421/react-native-hide-keyboard) (for now) is to wrap the TextInput in a [ScrollView](https://facebook.github.io/react-native/docs/scrollview.html#content), which lets you [dismiss the keyboard](https://facebook.github.io/react-native/docs/scrollview.html#keyboarddismissmode) if you hold the top of it and drag down.

It would really be great if multiline TextInputs had an onScroll property (or even better, a dismissKeyboardOnScroll property). But for now I will [probably just add a bar on the top of the keyboard](https://github.com/constellational/iOS/issues/50) to let you know that you can drag it down.