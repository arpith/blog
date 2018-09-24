---
layout: post
date: 2016-01-14 17:15:11 GMT
tags:
- markdown
- react
- reactjs
- writing
- notes
- Sharing
- distraction free
- constellational
- HTML
- plain text
- web development
- javascript
title: "Handling Markdown in React"
---
# Handling Markdown in React

<p>I’m <a href="http://github.com/constellational">building</a> an app to <a href="https://medium.com/@arpith/sharing-a-post-520659ff3857#.evkes1k2f">share</a> <a href="https://medium.com/@arpith/drafts-unpublished-edits-and-posts-b55fc5f1ead#.5src34972">notes</a> called <a href="http://constellational.com/">Constellational</a> (<a href="http://eepurl.com/bHN6Mf">sign up here!</a>) and one thing I’ve wanted is to support <a href="https://daringfireball.net/projects/markdown/">Markdown</a>. Markdown is a way to write text that looks sane, for example:</p><pre>#This is a headline</pre><p>That can be converted to the HTML for a headline:</p><pre>&lt;h1&gt;This is a headline&lt;/h1&gt;</pre><p>No one should have to write that by hand.</p><figure class="tmblr-full" data-orig-height="1536" data-orig-width="2048"><img src="/images/d80888bcab11ef28ddf48c7a122276f44bf0f6a793ae3dec7876c18898d58570.png" data-orig-height="1536" data-orig-width="2048"></figure><p>I’m using an NPM package called <a href="https://github.com/chjj/marked">marked</a> to do this conversion. Marked returns an HTML string — which will unfortunately be escaped by <a href="https://facebook.github.io/react/">React</a> <a href="https://facebook.github.io/react/docs/jsx-gotchas.html#html-entities">if you include in your JSX</a>.</p><p>To work around this we can <a href="https://facebook.github.io/react/tips/dangerously-set-inner-html.html">Dangerously Set InnerHTML</a>. Basically, we’re<a href="https://facebook.github.io/react/docs/tutorial.html#adding-markdown">relying on Marked to be secure</a> and escape any HTML it finds within the Markdown string that needs to be converted.</p><p>Our <a href="https://github.com/constellational/web/blob/44bc395233ae96c2a92d735aef2b568a8b756bc5/views.js">component</a> now looks like:</p><pre>import React from 'react';<br>import marked from 'marked';</pre><pre>class Post extends React.Component {<br> &nbsp;constructor(props, context) {<br> &nbsp; &nbsp;super(props, context);<br> &nbsp; &nbsp;this.rawMarkup = this.rawMarkup.bind(this);<br> &nbsp;}</pre><pre> &nbsp;rawMarkup() {<br> &nbsp; &nbsp;let rawMarkup = marked(this.props.data.data, {sanitize: true});<br> &nbsp; &nbsp;return { __html: rawMarkup };<br> &nbsp;}</pre><pre> &nbsp;render() {<br> &nbsp; &nbsp;var post = this.props.data;<br> &nbsp; &nbsp;return &lt;div className="post"&gt;<br> &nbsp; &nbsp; &lt;span dangerouslySetInnerHTML={this.rawMarkup()} /&gt;;<br> &nbsp; &nbsp;&lt;/div&gt;;<br> &nbsp;}<br>}</pre><p>This fixes <a href="https://github.com/constellational/web/issues/14">#14</a></p>