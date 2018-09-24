---
layout: post
date: 2015-12-05 16:41:06 GMT
tags:
- react native
- app
- ios app development
- flux
- writing
- reading
- universal links
- app indexing
- users
title: "Reading everyone you love"
---
# Reading everyone you love

<p>I’m <a href="http://github.com/constellational">building an app</a> to let you share your notes called <a href="http://constellational.com/">Constellational</a> (<a href="http://eepurl.com/bHN6Mf">sign up here</a> if you want this). Earlier I had talked about <a href="https://medium.com/@arpith/drafts-unpublished-edits-and-posts-b55fc5f1ead#.r7cunm9kg">what kinds of posts</a> my app supports, and yesterday I wrote about what goes into <a href="https://medium.com/@arpith/sharing-a-post-520659ff3857#.5i6pfn9tr">sharing a post</a>. Right now posts are shared via a link that <a href="https://medium.com/@arpith/displaying-your-webpage-4dd545fc92f9#.ylewv46kb">leads to a webpage</a>, but I want to change that. I’m working on reading other people’s posts <a href="https://github.com/constellational/iOS/issues/51">in the app itself</a>(without having to go to their webpage). To do this, my <a href="https://facebook.github.io/flux/">flux</a> <a href="https://facebook.github.io/flux/docs/overview.html#stores">store</a> has to support multiple users (not just your posts).</p><figure class="tmblr-full" data-orig-height="600" data-orig-width="800"><img src="/images/b1fac6d5264605d08b79468078b00c2bfd85e0a30abe87385949b6d9498ba39d.jpg" data-orig-height="600" data-orig-width="800"></figure><p><br></p><p>As you can see from <a href="https://github.com/constellational/iOS/commit/7186c9d24e93a7c95d0fda574d0278b6e5da2ec6">this commit</a>, things changed quite a bit — however, the main idea is that _posts[username][postURL] is where posts stay, and _users[username] gets you the list of postURLs for that user. Otherwise, it’s pretty much the same code!</p><p>You do <a href="https://github.com/constellational/iOS/commit/9bdabb35fc06a7417cb4584591eabe80df832c32">need to initialise _posts[username]</a> (as an empty object) when fetching posts:</p><pre>function fetchFromServer(username) {<br> &nbsp;if (!username) username = SettingStore.getUsername();<br> &nbsp;return fetchUser(username).then((user) =&gt; {<br> &nbsp; &nbsp;_users[username] = user;<br> &nbsp; &nbsp;var promiseArr = user.posts.map(url =&gt; fetchPost(username, url));<br> &nbsp; &nbsp;return Promise.all(promiseArr);<br> &nbsp;}).then((posts) =&gt; {<br> &nbsp; &nbsp;if (!_posts[username]) _posts[username] = {};<br> &nbsp; &nbsp;posts.map((post) =&gt; {<br> &nbsp; &nbsp; &nbsp;_posts[username][post.url] = post;<br> &nbsp; &nbsp;});<br> &nbsp; &nbsp;PostStore.emitChange();<br> &nbsp;});<br>}</pre><p>Still to come: <a href="https://github.com/constellational/iOS/issues/51">actually handling URL’s for other users</a>! This is one step in the quest towards <a href="https://medium.com/@arpith/your-baby-s-first-word-will-be-json-bf341f690327#.wd2l4u4eq">universal links</a> and <a href="https://github.com/constellational/iOS/issues/28">Google app indexing</a>.</p>