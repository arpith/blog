---
layout: post
date: 2015-11-26 20:12:56 GMT
tags:
- react native
- writing
- apps
- distraction free
- minimal
- ui
- regular expressions
- regex
- word count
- text input
- javascript
title: "Everybody stand back, I know Regular Expressions!"
---
# Everybody stand back, I know Regular Expressions!

##(Spoiler alert: I don’t)

A few days ago, I talked about [adding a full screen text input](http://arpith.co/post/133875065157/react-native-full-screen-textinputs) to my [app](github.com/constellational), to make the experience of writing notes a little more pleasant. Today I’m adding a word count to my app, based on [this answer](http://stackoverflow.com/a/27418136) on [Stack Overflow](http://stackoverflow.com/):

    var s = 'your long string with many words. and then a few word-with hy-phens. ';
    
    // ["your", "long", "string", "with", "many", "words.", "and", "then", "a", "few", "word-with", "hy-phens.", ""]
    
    var wordCount = s.split(/\s+/).filter(w => (!!w)).length;
    
    // 12, since the "" at the end of the array has been filtered out

We’re using the [regular expression](https://xkcd.com/208/) to [split the string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split), and [filtering](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) out empty “words”. Adding this code to the [React Native](http://facebook.github.io/react-native/) component is straightforward — in our page’s [constructor](https://facebook.github.io/react/docs/reusable-components.html#es6-classes), we’ll add the following:

    this.updateKeyboardSpace = (frames) => {
      var change;
      if (frames.endCoordinates) change = frames.endCoordinates.height;
      else change = frames.end.height;
      this.setState({height: this.state.height — change, isKeyboardUp: true});
    };
    this.resetKeyboardSpace = () => this.setState({height: this.state.fullHeight, isKeyboardUp: false});
    this.updateWordCount = (text) => this.setState({wordCount: text.split(/\s+/).filter(w => !!w).length});

Our render function [now looks like](https://github.com/constellational/iOS/blob/fba88515dfe49636400345d0e8f28caf8a74c9be/components/EditPage.js):

    render() {
      if (this.state.isKeyboardUp) var title = <text></text>;
      else var title = <text style="{styles.title}">{this.state.wordCount} words</text>;
          
      return (
        <view style="{styles.page}" onlayout="{(ev)"> {
          // 80 is for the navbar on top
          var fullHeight = ev.nativeEvent.layout.height — 80;
          this.setState({height: fullHeight, fullHeight: fullHeight});
        }}>
            
          <navbar leftbutton="{this.cancelButton}" title="{title}" rightbutton="{this.postButton}/"><scrollview keyboarddismissmode="interactive"><textinput multiline="{true}" onchangetext="{(text)"> {
                this.state.post.data = text;
                this.updateWordCount(text);
              }}
              defaultValue={this.state.post.data}
              autoFocus={true}
              style={[styles.input, {height:this.state.height}]}
            />
          </textinput></scrollview></navbar></view>
      );
    }

There are two things to look at here: (a) we only display the word count when the keyboard is down, and (b) we update the word count when the TextInput’s text changes. This fixes [#43](https://github.com/constellational/iOS/issues/43).