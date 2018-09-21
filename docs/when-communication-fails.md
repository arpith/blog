---
layout: post
date: 2015-11-25 17:38:59 GMT
tags:
- react native
- flux
- optimistic ui
- constellational
- es2015
- promises
- failure
- writing
- trying again
title: "When Communication Fails"
---
# When Communication Fails

##Telling a server you’ve optimistically created a post

Last week, I talked about how using [optimistic UI improves my app’s performance](http://arpith.co/post/133536963842/speed-up-your-app-with-optimistic-ui), and how [deletes can be retried](http://arpith.co/post/133664864052/optimism-in-ui-can-it-go-too-far) until the [server](http://github.com/constellational/api) responds with a success. Today’s story is about ensuring that the server state reflects the posts that were created or edited optimistically on the [app](http://github.com/constellational/iOS).

As before, we’re storing the requests to be made (create, edit, delete) in a list along with a callback — this is useful when creating or editing a post, because once the request has been made successfully we want to replace the ‘fake’ post (that we had stored and displayed to the user in the app) with the ‘real’ post (that the server responded with). One key difference is that the post’s url will change, because the posts are served via [cloudfront](https://aws.amazon.com/cloudfront/), and the url contains a [version id](http://docs.aws.amazon.com/AmazonS3/latest/dev/ObjectVersioning.html) that is generated (and returned) by the server.

##Keeping track of requests

[This is how we add a request](https://github.com/constellational/iOS/blob/3e97fda0fdf28dd7c08edac5f7f8cb7bd2b59ffe/stores/PostStore.js):

    var _requests = [];
    function addRequest(url, params, callback) {
      _requests.push({url: url, params: params, callback: callback});
      retryFailedRequests();
    }
    
Nothing fancy, but note that we retry failed requests when we add a new request.

##Retrying failed requests

[This is the code](https://github.com/constellational/iOS/blob/3e97fda0fdf28dd7c08edac5f7f8cb7bd2b59ffe/stores/PostStore.js) that retries failed requests:

    function retryFailedRequests() {
      var promiseArr = _requests.map((req) => {
        if (req.stat === 'succeeded') return req;
        else return fetch(req.url, req.params).then(res => res.json()).then(req.callback).then(() => {
          req.stat = 'succeeded';
          return req;
        }).catch(() => {
          req.stat = 'failed';
          return req;
        });
      });
      Promise.all(promiseArr).then((res) => {
        _requests = res;
      });
    }
    
Basically it maps the array of requests into a new array where each element is either the request itself (if it was successful) or a [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) to retry the request (if its status was failed). This Promise will return the request with its status updated, which will allow us to use [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) to generate the updated array of requests.

##Updating the local store with the server’s response

You might have noticed a `req.callback` in there (after retrying the request and before updating its status). This comes in handy when we need to replace a post with the server’s response:

    function replacePost(oldPost, newPost) {
      delete _posts[oldPost.url];
      var i = _postURLs.indexOf(oldPost.url);
      _postURLs.splice(i, 1);
      _postURLs.unshift(newPost.url);
      _posts[newPost.url] = newPost;
      PostStore.emitChange();
      return updateAsyncStore();
    }
    
##Optimistically creating a post

Now that we’ve got that in place, creating a post looks like this:

    function createPost(post) {
      // Create a temporary post
      post.created = new Date().toISOString();
      post.updated = post.created;
      if (!post.id) post.id = post.created;
      post.key = post.created;
      post.url = post.key;
      
      // Store it locally
      _postURLs.unshift(post.url);
      _posts[post.url] = post;
      PostStore.emitChange();
      
      // Create a request
      var username = SettingStore.getUsername();
      var url = APIURL + ‘/’ + username;
      post.token = SettingStore.getToken();
      var params = {method: ‘POST’, body: JSON.stringify(post), headers: HEADERS};
      var callback = (createdPost) => replacePost(post, createdPost);
      
      // Add it to the list of requests
      addRequest(url, params, callback);
    }
    
Note that when we add this request (to create a post) to our list of requests, we follow that up by retrying failed requests — which includes this one, as its status is not yet ‘succeeded’. This fixes [#46](https://github.com/constellational/iOS/issues/46).