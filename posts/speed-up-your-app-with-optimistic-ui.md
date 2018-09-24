---
layout: post
date: 2015-11-19 17:13:44 GMT
tags:
- flux
- react native
- apps
- optimistic ui
- api
- stores
- constellational
title: "Speed Up Your App With Optimistic UI"
---
# Speed Up Your App With Optimistic UI

[My app’s](http://github.com/constellational) [flux](https://facebook.github.io/flux/) [store](https://facebook.github.io/flux/docs/overview.html#stores) [used to do this](https://github.com/constellational/iOS/blob/e8a6969da02fd78915cbc3d572144f56ab678559/stores/PostStore.js) when deleting a post:

    function deletePost(post) {
      // Create the API request
      var username = SettingStore.getUsername();
      var token = SettingStore.getToken();
      var body = JSON.stringify({token: token});
      var key = post.key;
      if (!key) key = post.created + post.id;
      var url = APIURL + ‘/’ + username + ‘/’ + key;
      var params = {method: ‘DELETE’, body: body, headers: HEADERS};
      
      // Make the API request
      fetch(url, params).then(() => {
        // Delete the post locally
        var i = _postURLs.indexOf(post.url);
        _postURLs.splice(i, 1);
        delete _posts[post.url];
        PostStore.emitChange();
        return updateAsyncStore();
      });
    }
    
Basically, the [app](http://github.com/constellational/iOS) tells the [API](http://github.com/constellational/api) to [delete the post](https://github.com/constellational/api/blob/master/delete/index.js), and when it gets the response, it removes the post from its list of posts. This is then reflected in the UI — which means the user has to wait for the API to respond to see the post removed from the list!

Let’s be optimistic instead, and remove the post from the local list first (before using the API to delete the online copy). This is what [the code looks like now](https://github.com/constellational/iOS/blob/74b6c9dbd05bfcc0b0fc8480c50d0df87ce4b7b0/stores/PostStore.js):

    function deletePost(post) {
      // Delete the post locally
      var i = _postURLs.indexOf(post.url);
      _postURLs.splice(i, 1);
      delete _posts[post.url];
      PostStore.emitChange();
      
      // Create and make the API request
      var username = SettingStore.getUsername();
      var token = SettingStore.getToken();
      var body = JSON.stringify({token: token});
      var key = post.key;
      if (!key) key = post.created + post.id;
      var url = APIURL + ‘/’ + username + ‘/’ + key;
      var params = {method: ‘DELETE’, body: body, headers: HEADERS};
      fetch(url, params).then(updateAsyncStore);
    }
    
Two things need to be worked out, (a) what happens when the API call fails and (b) how do you do this for creating or editing posts. Let me know if you have ideas!