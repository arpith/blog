---
layout: post
date: 2015-11-15 17:24:28 GMT
tags:
- first website
- web app
- nodejs
- javascript
- heroku
- json
- hello world
- apps
- github
- learn to code
- ios9
- universal links
- email signin
title: "Your Baby’s First Word Will Be JSON"
---
I ended [yesterday’s post](http://arpith.co/post/133201714402/using-a-link-to-sign-in-to-your-app) (on using a link to sign in to an app) by promising to try out iOS 9’s [Universal Links](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1). To do this, you need to serve a JSON file at yourDomain.com/apple-app-site-association — this is a quick way to do that.

There are two steps to this:
1. [Create a repository](https://help.github.com/articles/create-a-repo/) on [GitHub](http://github.com/) with code to serve the JSON file
2. Create an app on [Heroku](http://heroku.com/) that [runs the code in that repository](https://devcenter.heroku.com/articles/github-integration)

[The code](https://github.com/constellational/web/blob/2ef137b0a2bc6b4aded4c166ac5e3adccd757b17/index.js) we’re interested in is [pretty much straight off](https://nodejs.org/en/about/) the [node.js](http://nodejs.org/) website:

    var http = require(‘http’);
    var json = {
      “applinks”: {
        “apps”: [],
        “details”: [
          { 
            “appID”: “5C79KQUVVH.com.constellational”,
            “paths”: [ “*” ]
          }
        ]
      }
    };
    
    var port = process.env.PORT || 3000;  
    http.createServer(function (req, res) {
      res.writeHead(200, {‘Content-Type’: ‘application/json’});
      res.end(JSON.stringify(json));
    }).listen(port);
    
    
To get this running on Heroku, we also need an [NPM](http://npmjs.com/) [package.json](https://docs.npmjs.com/files/package.json):

    { 
      “name”: “constellational-web”,
      “version”: “1.0.0”,
      “main”: “index.js”,
      “scripts”: {
        “start”: “node index.js”
      }
    }
    
An [app.json](https://github.com/constellational/web/blob/2ef137b0a2bc6b4aded4c166ac5e3adccd757b17/app.json):
    
    {
      “name”: “Constellational”,
      “repository”: “https://github.com/constellational/web"
    }
    
And a [Procfile](https://github.com/constellational/web/blob/2ef137b0a2bc6b4aded4c166ac5e3adccd757b17/Procfile):

    web: npm start

And that’s it! You’ve made your first web app! Make sure your app on Heroku is [connected to your Github repository](https://devcenter.heroku.com/articles/github-integration#enabling-github-integration), and click on [“Deploy Branch”](https://devcenter.heroku.com/articles/github-integration#manual-deploys) at the bottom of the deploy page.