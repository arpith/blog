---
layout: post
date: 2015-11-27 18:26:24 GMT
tags:
- flux
- react native
- apps
- ios
- writing
- drafts
- editing
- posts
- notes
- constellational
title: "Drafts, Unpublished Edits, and Posts"
---
<h2>How Constellational supports them all</h2>

<p>I’m building an <a href="http://github.com/constellational/iOS">app</a> called <a href="http://github.com/constellational">Constellational</a> that lets you publish your notes online. This is the story of the how a post goes from draft to published to edited to updated.</p>

<p>There are three kinds of posts that the app currently supports: drafts, posts with unpublished edits, and regular posts (that can be viewed on the website). But to start with, you tap the create post button and the <a href="http://arpith.co/post/133875065157/react-native-full-screen-textinputs">editing page</a> opens up. Tap cancel, and you go back to the list of posts. If you’ve typed something before you tapped cancel, this gets saved as a draft post (and appears on your list of posts, but marked <i>draft</i>). Let’s open that post up again to edit it. Tapping cancel again will just update the draft’s content (if you made any changes) whereas tapping Post will publish it.</p>

<figure data-orig-width="540" data-orig-height="405" data-orig-src="/images/0d6952958c059f56309e5bfa7e6983dc9afdc54136e2df786684d1dc8ea5e43e.png" class="tmblr-full"><img src="/images/51bb974d107fb8cac7feeee3ea62e9d806cfa66975b4ccaf217280f002d63f9b.png" data-orig-width="540" data-orig-height="405" data-orig-src="/images/0d6952958c059f56309e5bfa7e6983dc9afdc54136e2df786684d1dc8ea5e43e.png"></figure><p>Now let’s edit a post that you’ve published. Same procedure, you tap on the post in your posts page and choose edit to bring up the editing page. Notice that the Post button now says Update. If you make a change and tap cancel, this time the post will be marked <i>editing</i> — you’ve basically got unpublished edits. When you’re ready to publish these changes, tap on the post, choose edit, and tap on Update.</p>

<p><a href="https://github.com/constellational/iOS/blob/1554cb80455db9d9809519f8eb13a21c306b4143/components/EditPage.js">Behind the scenes</a>, the cancel button does a lot of work:</p>

<pre><code>saveDraft() {
  if (this.state.post.data !== this.initialData) {
    if (this.state.post.isDraft) {
      DraftActions.edit(this.state.post);
    } else {
      if (this.isEditing) {
        this.state.post.hasUnpublishedEdits = true;
        EditActions.save(this.state.post);
      } else {
        this.state.post.isDraft = true;
        DraftActions.create(this.state.post);
      }
    }
  this.props.navigator.pop();
}
</code></pre>

<p>Basically, it first checks that the post content has actually changed and then if the post was a draft, it edits it with the new content. If it hasn’t already been marked as a draft, it’s either a new post (in which case it marks it and stores it as a draft) or a post that’s being edited. In this case the post is marked as having unpublished edits before being saved.</p>

<p>This brings us to the Post button — again, it’s easiest to make sense of <a href="https://github.com/constellational/iOS/blob/1554cb80455db9d9809519f8eb13a21c306b4143/components/EditPage.js">the code</a> by going through it line by line:</p>

<pre><code>savePost() {
  if (this.isEditing &amp;&amp; this.state.post.isDraft) {
    this.state.post.isDraft = false;
    DraftActions.del(this.state.post);
    delete this.state.post.id;
    PostActions.create(this.state.post);
  } else if (this.isEditing &amp;&amp; !this.state.post.isDraft) {
    if (this.state.post.hasUnpublishedEdits) {
      this.state.post.hasUnpublishedEdits = false;
      EditActions.del(this.state.post);
    }
    PostActions.edit(this.state.post);
  } else {
    PostActions.create(this.state.post);
  }
  this.props.navigator.pop();
}
</code></pre>

<p>The function first checks if you’re editing a post and it has been marked as a draft. This means it has to unmark it as a draft, delete the draft, and create the post (this involves making a request to the server). If you’re editing a post and it isn’t a draft, it first checks if it had unpublished edits, in which case it deletes the local copy (with edits), removes the flag (hasUnpublishedEdits) and edits the post (again, this involves a request to the server). If none of those conditions were true, you’re basically creating a post — this is straightforward.</p>

<p>All these <a href="https://facebook.github.io/flux/">flux</a> <a href="https://facebook.github.io/flux/docs/actions-and-the-dispatcher.html#content">actions</a> correspond to <a href="https://facebook.github.io/flux/docs/overview.html#stores">stores</a>, which means listing your posts (and drafts, and posts with unpublished changes) <a href="https://github.com/constellational/iOS/blob/1554cb80455db9d9809519f8eb13a21c306b4143/components/PostsPage.js">looks like this</a>:</p>

<pre><code>getAll() {
  var posts = PostStore.getAll();
  posts = posts.map((post) =&gt; {
    var unpublishedEdits = EditStore.get(post.id);
    if (unpublishedEdits) {
      post = unpublishedEdits;
      post.hasUnpublishedEdits = true;
    }
    return post;
  });
  var drafts = DraftStore.getAll();
    var all = posts.concat(drafts);
    var sorted = all.sort((a, b) =&gt; {
    if (a.updated &gt; b.updated) return -1;
    else if (a.updated &lt; b.updated) return 1;<br> &nbsp; &nbsp;else return 0;<br> &nbsp;}); &nbsp;return sorted;<br>}</code></pre><pre><br></pre><p><br></p><p>Note that the function checks each post to see if it has unpublished edits, and if it does, it replaces the post (in the list) with the one with the changes (and marks it). It then adds the drafts to the list, and sorts the list based on the updated timestamp of the posts.</p>