---
layout: post
date: 2015-11-14 16:09:13 GMT
tags:
- ios
- deep links
- apps
- signin
- tokens
- custom url schemes
- universal links
- email sign in
title: "Using a Link to Sign In to Your App"
---
# Using a Link to Sign In to Your App

I wrote a [short post](http://arpith.co/post/133135590507/signing-in-with-email) yesterday about how [the app I’m building](http://github.com/constellational) sends its [sign in emails](https://github.com/constellational/api/blob/master/signin/index.js). This is the other side of the story, how that link in the email signs you into the app.

The link itself isn’t very special, you create a [custom URL scheme](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html) for your app and then use it instead of the usual `https://` — for example, my signin url looks like `constellational://signin?token=TOKEN`. What makes this interesting is that apps like Gmail’s Inbox (or the Gmail app itself) don’t seem to support links that look like that. There’s an easy work around, you create a page (like https://constellational.com) that contains the crazy looking url, and send that link instead.

This brings us to [Universal Links](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1), which lets you tell iOS 9 that you’d rather have the app open up directly instead of having to make the user go to the webpage (and click on the link to the app). The idea is that you add a small JSON file to your website, and add the domain to your app’s list of associated domains. Sounds simple, right? But! This is the part I haven’t tried out yet - stay tuned for the sequel to this post, tomorrow.