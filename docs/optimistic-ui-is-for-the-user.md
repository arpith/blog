---
layout: post
date: 2015-11-20 17:30:52 GMT
tags:
- optimistic ui
- flux
- react native
- apps
- ios
- javascript
- constellational
title: "Optimistic UI is For the User"
---
In yesterday’s post on [speeding up your app using optimistic UI](http://arpith.co/post/133536963842/speed-up-your-app-with-optimistic-ui), I described how you can improve the perceived performance of your app when it comes to deleting posts. Today I’ll be talking about the next step, speeding up the creating and editing of posts.
Once again, the code we’re interested in is the [flux](https://facebook.github.io/flux/) [store](https://facebook.github.io/flux/docs/overview.html#stores). Let’s look at editing posts to begin with, because that is conceptually simpler. [The original code](https://github.com/constellational/iOS/blob/74b6c9dbd05bfcc0b0fc8480c50d0df87ce4b7b0/stores/PostStore.js) looked like:

    function editPost(post) {
      
      // Set up API call to edit post
      var username = SettingStore.getUsername();
      var key = post.key;
      if (!key) key = post.created + post.id;
      var url = APIURL + ‘/’ + username + ‘/’ + key;
      post.token = SettingStore.getToken();
      var params = {method: ‘PUT’, body: JSON.stringify(post), headers: HEADERS};
      
      // Make the API call
      fetch(url, params).then(res => res.json()).then((post) => {
        
        // Add the post that's returned to the list of posts
        var i = _postURLs.indexOf(post.url);
        _postURLs.splice(i, 1);
        _postURLs.unshift(post.url);
        _posts[post.url] = post;
        PostStore.emitChange();
        return updateAsyncStore();
      });
    }
    
  
To speed things up from the user’s perspective, we’ll [save the edited post to our list of posts](https://github.com/constellational/iOS/blob/f1f7ff1031b5613a928f2884db20d385679e3211/stores/PostStore.js) before storing it online [using the API](https://github.com/constellational/api/blob/master/edit/index.js):

    
    function editPost(post) {
      // Add the edited post to our list
      var i = _postURLs.indexOf(post.url);
      var initialURL = post.url;
      _postURLs.splice(i, 1);
      _postURLs.unshift(post.url);
      _posts[post.url] = post;
      PostStore.emitChange();
      
      // Set up the API call
      var username = SettingStore.getUsername();
      var key = post.key;
      if (!key) key = post.created + post.id;
      var url = APIURL + ‘/’ + username + ‘/’ + key;
      post.token = SettingStore.getToken();
      var params = {method: ‘PUT’, body: JSON.stringify(post), headers: HEADERS};
      
      // Make the API request
      fetch(url, params).then(res => res.json()).then((post) => {
        
        // Delete that post we created earlier
        // And replace it with the data returned by the API
        delete _posts[initialURL];
        _postURLs.splice(i, 1);
        _postURLs.unshift(post.url);
        _posts[url] = post;
        PostStore.emitChange();
        return updateAsyncStore();
      });
    }
    
As you can see, our code has approximately doubled, both in length and complexity. But the app is now much faster, avoiding a HTTP request that blocks the updating of the UI. It also fixes the weird effect of a “missing” post (earlier, you would edit a post, and it would take some time for the edit to be reflected).

[The code to speed up creating a post](https://github.com/constellational/iOS/blob/2481043ba56b51b3a642de9177b08ca05ce08296/stores/PostStore.js) is similar, you create the post with fake data, and delete it once you get the actual post data [from the API response](https://github.com/constellational/api/blob/master/create/index.js).


    function createPost(post) {
      
      // Create and store the post with fake data
      post.created = new Date().toISOString();
      post.updated = post.created;
      if (!post.id) post.id = post.created;
      post.key = post.created;
      post.url = post.key;
      _postURLs.unshift(post.url);
      _posts[post.url] = post;
      PostStore.emitChange();
      
      // Create and make the API call
      var username = SettingStore.getUsername();
      var url = APIURL + ‘/’ + username;
      post.token = SettingStore.getToken();
      var params = {method: ‘POST’, body: JSON.stringify(post), headers: HEADERS};
      fetch(url, params).then(res => res.json()).then((createdPost) => {
      
        // Delete the post with fake data
        // And replace it with the actual created post
        delete _posts[post.url];
        var i = _postURLs.indexOf(post.url);
        _postURLs.splice(i, 1);
        _postURLs.unshift(createdPost.url);
        _posts[createdPost.url] = createdPost;
        PostStore.emitChange();
        return updateAsyncStore();
      });
    }
    
Note that I still haven’t figured out how to handle failures — perhaps that’s best left for another day.