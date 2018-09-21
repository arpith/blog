---
layout: post
date: 2015-11-22 17:38:53 GMT
tags:
- nodejs
- react native
- apps
- web development
- signup
- usernames
- toLowerCase
- javascript
title: "You say username, I say Username"
---
# You say username, I say Username

When a user signs up for the [app I’m building](https://github.com/constellational), one of the things to be checked is that the username is available. This is done by [creating a file](https://github.com/constellational/api/blob/c9909b985dc6f9bee156d3983fe04c0892b44a4a/signup/index.js) (on S3) every time a user signs up, and [checking if this file exists](https://github.com/constellational/iOS/blob/c166624349d28ca1124fc5e9289931b188e17b20/stores/SettingStore.js) before proceeding with the signup process.

    checkUsername(username) {
      var username = username.toLowerCase();
      this.setState({username: username});
      var url = URL + '/' + username;
        return fetch(url).then((res) => {
        if (res.status !== 404) {
          this.setState({
            isUsernameAvailable: false,
            heading: 'Try another username',
            subheading: 'This one\'s taken!'
          });
        } else {
          this.setState({
            isUsernameAvailable: true,
            heading: 'Almost Done!',
            subheading: 'What\'s your email address?'
          });
        }
      });
    }
    
Note that we’re checking if the lower case version of the username is available, this is to prevent mixups like one user signing up as Username and another as username. This behaviour is replicated on the server, the [API](http://github.com/constellational/api) converts the username that has been supplied to lowercase before (for example) [creating a post](https://github.com/constellational/api/blob/c9909b985dc6f9bee156d3983fe04c0892b44a4a/create/index.js) — in case the app has stored the username as Username.

    function create(username, token, post) {
      var username = username.toLowerCase();
      delete post.token;
      return auth(username, token).then(function() {
        var bucket = 'constellational-store';
        post.created = new Date().toISOString();
        post.updated = post.created;
        if (!post.id) post.id = randomString();
        post.key = post.created + post.id;
        return putJSON(bucket, username + '/' + post.key, post).then(function(data) {
         post.url = post.key + '?VersionId=' + data.VersionId;
          return post;
        });
      });
    }
    
This fixes [issue #4](https://github.com/constellational/api/issues/4).