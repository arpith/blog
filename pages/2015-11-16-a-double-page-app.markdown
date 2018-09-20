---
layout: post
date: 2015-11-16 17:30:37 GMT
tags:
- nodejs
- javascript
- static site
- heroku
- deep links
- universal links
- custom url schemes
- email signin
- koa
- koa-static
- apps
title: "A Double Page App"
---
So far we’ve seen [how to use a webpage to sign into an app](http://arpith.co/post/133201714402/using-a-link-to-sign-in-to-your-app) and how we can use [Universal Links](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1) to skip [the step of opening the webpage](http://arpith.co/post/133275553207/your-babys-first-word-will-be-json) — and open the app directly from the [sign in email](http://arpith.co/post/133135590507/signing-in-with-email). It makes sense to support both methods of signing in, so today we’ll use [koa-static](https://github.com/koajs/static) to serve both the [JSON file](https://github.com/constellational/web/blob/7d09b79def0ab6ce5c61b5656f33ef1545c6287c/public/apple-app-site-association) as well as the [HTML file that contains the link to open the app](https://github.com/constellational/web/blob/7d09b79def0ab6ce5c61b5656f33ef1545c6287c/public/index.html).

[Our code](https://github.com/constellational/web/blob/7d09b79def0ab6ce5c61b5656f33ef1545c6287c/index.js) now looks like:
    
    var serve = require(‘koa-static’);
    var koa = require(‘koa’);
    var app = koa(); 
    var port = process.env.PORT || 3000; 
    app.use(serve(‘public’));
    app.listen(port);


The first thing you might notice is that it’s a bit shorter than it was yesterday, and that’s partly because we’ve moved the JSON into a separate file in our [public directory](https://github.com/constellational/web/tree/7d09b79def0ab6ce5c61b5656f33ef1545c6287c/public). This is also where the HTML file is. All that needs to be done is to modify our [package.json](https://github.com/constellational/web/blob/7d09b79def0ab6ce5c61b5656f33ef1545c6287c/package.json) file to look like:

    { 
      "name": "constellational-web",
      "version": "1.0.0",
      "main": "index.js",
      "scripts": {
        "start": "node index.js"
      },
      "dependencies": {
        "koa": "*",
        "koa-static": "*"
      }
    }
    
This tells [Heroku](http://heroku.com/) to install the two packages we used in our code. Click on “[Deploy Branch](https://devcenter.heroku.com/articles/github-integration#manual-deploys)” at the bottom of your app’s deployment page on Heroku and you’re done. Your website now serves [a page that lets you open the app](https://medium.com/@arpith/using-a-link-to-sign-in-to-your-app-46bb075e2554) (https://constellational.com) and it also serves an [Apple App Site Association file](https://medium.com/@arpith/your-baby-s-first-word-will-be-json-bf341f690327) (https://constellational.com/apple-app-site-association).

Bonus: If you “[Enable Automatic Deploys](https://devcenter.heroku.com/articles/github-integration#automatic-deploys)”, Heroku will re-deploy your app when you make changes to files on GitHub, which I find useful.