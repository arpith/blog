---
layout: post
date: 2015-11-17 19:10:07 GMT
tags:
- reactjs
- isomorphic
- webpack
- npm
- heroku
- fetch api
- koa
- koa-static
- nodejs
- node-fetch
title: "Isomorphic React in Three Simple Steps"
---
An isomorphic react app has three basic parts:

1. You generate a html file using [`react-dom/server`](https://www.npmjs.com/package/react-dom)’s [`reactDOM.renderToString()`](https://facebook.github.io/react/docs/top-level-api.html#reactdomserver.rendertostring)
2. This is then served along with the client javascript file —
3. — which is pretty much the same as the server-side code, except that it uses [`reactDOM.render()`](https://facebook.github.io/react/docs/top-level-api.html#reactdom.render)

The [code we’re interested in](https://github.com/constellational/web/blob/658bf66fd527a5e6c807c79f2f2df2dffcd342cf/index.js) on the server is:

    app.use(function *() {
      var splitURL = this.url.split('/');
      splitURL.shift();
      var username = splitURL.shift();
      var id = splitURL.shift();
      var user = yield fetchUser(username).then((user) => {
        if ((id) && (user.posts.indexOf(id) > 0)) {
          user.posts.splice(user.posts.indexOf(id), 1);
          user.posts.unshift(id);
        }
        var promiseArr = user.posts.map(id => fetchPost(username, id));
        return Promise.all(promiseArr).then((data) => {
          user.posts = data;
          return user;
        });
      });
      this.body = generateHTML(user);
    });
    
We’re still [serving static files off the public directory](http://arpith.co/post/133345400442/a-double-page-app), which brings us to the [client code](https://github.com/constellational/web/blob/f1c9b9d8b14072e0f69bb16f82a9dd4d83fe89dc/main.js):

    var splitPathname = window.location.pathname.split('/');
    splitPathname.shift();
    let username = splitPathname.shift();
    let id = splitPathname.shift();
    function load(username, id) {
      return fetchUser(username).then((user) => {
        if ((id) && (user.posts.indexOf(id) > 0)) {
          user.posts.splice(user.posts.indexOf(id), 1);
          user.posts.unshift(id);
        }
        var promiseArr = user.posts.map(id => fetchPost(username, id));
        Promise.all(promiseArr).then((data) => {
          user.posts = data;
          let mountNode = document.getElementById(“react-mount”);
          React.render(React.createElement(views.User, user), mountNode);
        });
      });
    }
    
Pretty similar, huh? The file is bundled using [webpack](https://webpack.github.io/) and put in the public directory using [this webpack.config.js](https://github.com/constellational/web/blob/90b46be61fb50eb09e035f19da83612e7c1c62bd/webpack.config.js):

    var path = require('path');
    var webpack = require('webpack'); 
    module.exports = {
      entry: [ 
        './main.js'
      ],
      output: { 
        path: path.join(__dirname, 'public'),
        filename: 'main.js',
      },
      module: {
        loaders: [{loader: ‘uglify’}, {
          exclude: /node_modules/,
          loader: 'babel' 
        }]
      },
      plugins: [ 
        new webpack.ProvidePlugin({
          ‘fetch’: ‘imports?this=>global!exports?global.fetch!whatwg-fetch’ 
        })
      ]
    };
    
Note that we’re using [whatwg-fetch](https://github.com/github/fetch) to support the [fetch api](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) on older browsers. We can run webpack using an [npm post install script](https://docs.npmjs.com/misc/scripts) in our [package.json](https://github.com/constellational/web/blob/658bf66fd527a5e6c807c79f2f2df2dffcd342cf/package.json):

    "scripts": { 
      "start": "node index.js",
      "postinstall": "webpack -p"
    }
    
To recap, we have

1. Server code that generates html and
2. Serves both the html and the client code
3. Which is pretty much the same as the server code

And that’s about it! Congratulations, you’ve made an isomorphic react app!