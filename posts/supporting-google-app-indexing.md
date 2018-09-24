---
layout: post
date: 2015-12-20 18:03:16 GMT
tags:
- constellational
- ios
- apps
- ios app development
- google app indexing
- search
- notes
- sharing
- reading
- writing
- itunes connect
- cocoapods
- universal links
title: "Supporting Google App Indexing"
---
# Supporting Google App Indexing

<p>I’m <a href="http://github.com/constellational">building an app</a> to <a href="http://arpith.co/post/134595293672/reading-everyone-you-love">share</a> <a href="http://arpith.co/post/134069674682/drafts-unpublished-edits-and-posts">notes</a> called <a href="http://constellational.com/">Constellational</a>, and recently I’ve&nbsp;<a href="http://arpith.co/post/135409935907/universal-links-in-react-native">added support for universal links</a>. This makes supporting <a href="https://developers.google.com/app-indexing/">Google App Indexing</a> straightforward. Here’s <a href="https://developers.google.com/app-indexing/ios/server">how you do it</a>.</p><h2>Installing with CocoaPods</h2><p>$ sudo gem install cocoapods</p><p>Then, in a new terminal window/tab:</p><pre>$ pod init</pre><p>This creates a <a href="https://guides.cocoapods.org/using/the-podfile.html">Podfile</a> — add the following to it:</p><pre>pod 'GoogleAppIndexing'</pre><p>And then install the pod:</p><pre>$ pod install</pre><p>If you got a warning about target overriding the ‘OTHER_LDFLAGS’ build setting, open the xcodeworkspace and <a href="http://stackoverflow.com/a/26077106">add $(inherited) as a new line in Build Settings’ Other Linker Flags</a>.</p><p>You’re also going to have to remember to use the xcodeworkspace instead of the .xcodeproj from now on :)</p><h2>Adding it to your app</h2><p>Add the header to the top of your AppDelegate.m</p><pre>#import &lt;GoogleAppIndexing/GoogleAppIndexing.h&gt;</pre><p>And call the register method in your application:didFinishLaunchingWithOptions: method:</p><pre>[[GSDAppIndexing sharedInstance] registerApp:your iTunes ID];</pre><p>To generate your iTunes ID you need to add your app to <a href="http://itunesconnect.apple.com/">iTunes Connect</a>.</p><p>That’s about it! This fixes <a href="https://github.com/constellational/iOS/issues/70">#70</a></p><p>Want to share your notes? <a href="http://eepurl.com/bHN6Mf">Sign up here</a>!</p>