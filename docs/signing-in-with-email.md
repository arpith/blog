---
layout: post
date: 2015-11-13 15:36:42 GMT
tags:
- nodejs
- email signup
- mandrill
- bcrypt
- amazon web services
- lambda
- s3
- medium
- promises
- bluebird
- app
- github
- code
title: "Signing In With Email"
---
I’m building a [small app](http://github.com/constellational) to post stuff online—just text, for now. You’ve probably heard of (and used) Medium’s email sign in, where they send you a link that lets you sign into the app (or the browser). This is the way I did it.

The code [is on GitHub](https://github.com/constellational/api/blob/master/signin/index.js), and to begin with, let’s take a look at this function:

    function signin(username, email) {
      var user;
      var bucket = ‘constellational-meta’;
      var token = {
        id: randomString(),
        secret: randomString() 
      };
      return checkEmail(username, email).then(function(details) {
        user = details;
        // Bcrypt temporary token
        return bcrypt.hashAsync(token.secret, 10);
      }).then(function(hash) {
        if (!user.tempTokens) user.tempTokens = {};
        user.tempTokens[token.id] = {hash: hash, created: Date.now()};
        // Store bcrypted hash of temporary token
        return putJSON(bucket, username, user);
      }).then(function() {
        // Send email
        return sendEmail(username, email, token);
      });
    }
    
    
Basically, you create a temporary token, store its hash and send it via email. The email is sent using [Mandrill](http://mandrill.com/), in this function:

    function sendEmail(username, email, token) {
      var escapedToken = encodeURIComponent(JSON.stringify(token));
      var link = APP_URL + ‘signin?token=’ + escapedToken;
      
      var message = {
        html: ‘<p>Hi there!</p><p>Click <a href="%E2%80%99" link>here to sign in</a></p>, 
        subject: ‘Sign in to Constellational’,
        from_email: ‘signin@constellational.com’,
        from_name: ‘Constellational Sign In’,
        to: [{ email: email, type: ‘to’ }],
        headers: { ‘Reply-To’: ‘arpith@constellational.com’ }
      };
      
      return new Promise(function(resolve, reject) {
        mandrill_client.messages.send({
          message: message
        }, function(res) {
          if (res[0].reject_reason) reject(res[0]);
          else resolve(res[0]);
        }, function(err) { 
          reject(err);
        });
      });
    }
    
    
That code looks messy, but all it’s doing is encoding the token (after converting it into a string) as a URI component, appending it to the app’s url and including it in the message sent via Mandrill.

The last few lines are in there because I couldn’t [promisify](https://github.com/petkaantonov/bluebird) Mandrill’s [send message function](https://mandrillapp.com/api/docs/messages.nodejs.html).

And that’s about it! Let me know if you have a better way of doing this - [I’m](http://twitter.com/arpith) [online](http://github.com/arpith) [everywhere](http://arpith@feedreader.co/)!

Bonus: This is the function that generates random strings, based on [this question on StackOverflow](http://stackoverflow.com/questions/8855687/secure-random-token-in-node-js) (using [this package](this package) that url-escapes base64 strings)

    function randomString() {
      var randomBytes = crypto.randomBytes(48);
      var randomBase64String = randomBytes.toString('base64');
      return base64url.escape(randomBase64String);
    }
    

That’s all folks!