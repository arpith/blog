---
layout: post
date: 2015-11-21 17:13:00 GMT
tags:
- flux
- react native
- stores
- optimistic ui
- app
- api
- code
- apps
- github
- javascript
- promises
title: "Optimism in UI: Can it go too far?"
---
# Optimism in UI: Can it go too far?

Previously, we had looked at using optimistic UI to improve the performance of [deleting](http://arpith.co/post/133536963842/speed-up-your-app-with-optimistic-ui), [creating and editing posts](http://arpith.co/post/133601783452/optimistic-ui-is-for-the-user) in my [app](http://github.com/constellational). The question was what should be done when a delete (for example) fails — we don’t want a difference between what you see on the [app](http://github.com/constellational/iOS) and what’s [published online](http://github.com/constellational/web).

My first thought was to just keep retrying the request until it succeeds. However, retrying the creation of a post shouldn’t result in duplicate posts. To fix this, I’ve changed the [API](http://github.com/constellational/api) (not really, I’ve [postponed this](https://github.com/constellational/api/issues/5) for now) to accept post metadata from the app, which will basically mean the API overwrites the stored post with the exact same data (for now, we can then avoid this extra write by [checking the contents of the post](https://github.com/constellational/api/issues/6) against the stored data before doing a rewrite).

In order to keep retrying the requests until they succeed, my [flux](https://facebook.github.io/flux/) [store](https://facebook.github.io/flux/docs/overview.html#stores) for [posts](https://github.com/constellational/iOS/blob/dceb8087fc2d4101932de1cd9a0fb071205786af/stores/PostStore.js) is also going to keep track of the requests made so far, and retry failed requests every time a new request is added to this list.

The code to delete posts [used to look like](https://github.com/constellational/iOS/blob/2481043ba56b51b3a642de9177b08ca05ce08296/stores/PostStore.js):

    function deletePost(post) {
      var i = _postURLs.indexOf(post.url);
      _postURLs.splice(i, 1);
      delete _posts[post.url];
      PostStore.emitChange();
      var username = SettingStore.getUsername();
      var token = SettingStore.getToken();
      var body = JSON.stringify({token: token});
      var key = post.key;
      if (!key) key = post.created + post.id;
      var url = APIURL + '/' + username + '/' + key;
      var params = {method: ‘DELETE’, body: body, headers: HEADERS};
      fetch(url, params).then(updateAsyncStore);
    }
    
With the new list of requests, [this now looks like](https://github.com/constellational/iOS/blob/dceb8087fc2d4101932de1cd9a0fb071205786af/stores/PostStore.js):

    function retryFailedRequests() {
      var promiseArr = _requests.map((req) => {
        if (req.stat !== 'succeeded') {
          return fetch(req.url, req.params)
            .then(res => res.json())
            .then(req.callback).then(() => {
              req.stat = 'succeeded';
              return req;
            }).catch(() => {
              req.stat = 'failed';
              return req;
            });
        } else {
          return req;
        }
      });
      Promise.all(promiseArr).then((res) => {
        _requests = res;
      });
    }
        
    function addRequest(url, params, callback) {
      _requests.push({url: url, params: params, callback: callback});
      retryRequests();
    }
    
    function deletePost(post) {
      var i = _postURLs.indexOf(post.url);
      _postURLs.splice(i, 1);
      delete _posts[post.url];
      PostStore.emitChange();
      var username = SettingStore.getUsername();
      var token = SettingStore.getToken();
      var body = JSON.stringify({token: token});
      var key = post.key;
      if (!key) key = post.created + post.id;
      var url = APIURL + '/' + username + '/' + key;
      var params = {method: ‘DELETE’, body: body, headers: HEADERS};
      addRequest(url, params, updateAsyncStore);
    }
    
As you can see, this is already getting a bit out of hand (take a look at [the file](https://github.com/constellational/iOS/blob/dceb8087fc2d4101932de1cd9a0fb071205786af/stores/PostStore.js) and you’ll get an idea of how complicated the methods to create/edit posts have become). Perhaps it would be simpler to treat the local state as the source of truth, and have a small method on the API that accepts a state object and updates it’s stored state appropriately. [Of course](arpith.co), [I’d](github.com/arpith) [love to hear](medium.com/@arpith) [your suggestions](instagram.com/siromoney) or [comments](twitter.com/arpith).