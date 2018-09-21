---
layout: post
date: 2015-11-23 17:22:55 GMT
tags:
- react native
- constellational
- ios app development
- san francisco font
- ios9
- tex input
- text editing
title: "San Francisco in React Native"
---
# San Francisco in React Native

The [app I’m building](http://github.com/constellational) lets you post notes online, so a major part of it is the text editor. Today I learnt that to [use San Francisco](https://github.com/facebook/react-native/issues/1611) (the default font on iOS 9) in a [React Native](http://facebook.github.io/react-native/) component, you need to [set fontFamily to ‘System’](https://github.com/constellational/iOS/issues/48).

    var styles = StyleSheet.create({
      input: {
        padding: 20,
        paddingTop: 10,
        fontSize: 18,
        fontFamily: 'System',
      },
    });
    
Also, fontSize 18 seems to be a nice balance between readability and aesthetics.

Still to come: [dismissing the keyboard on scroll](https://github.com/constellational/iOS/issues/47).