---
layout: post
date: 2015-06-10 06:02:51 GMT
tags:
- babeljs
- es6
- nodejs
- SSE
- eventsource
- promises
- classes
title: "Babel-node, ES6, and how I finally used promises"
---
# Babel-node, ES6, and how I finally used promises

<p>I&rsquo;ve been working on an <a href='https://github.com/runway7/satellite'>SSE server</a> and wanted to test how many connections it could accept. The <a href="https://github.com/aslakhellesoy/eventsource-node">eventsource library</a> seemed like a simple way to create SSE clients, so I wrote a small <a href="https://github.com/arpith/sse-test">nodejs app</a> to create a bunch of connections to a channel, send a message and print &lsquo;WORKS&rsquo; if they all got it. You can run it by cloning the repo (<code>$ git clone https://github.com/arpith/sse-test</code>) and then, in the directory, running <code>$ <a href="https://babeljs.io/docs/usage/cli/#babel-node">babel-node</a> main.js</code>  </p>

<pre>
<code>    
    var EventSource = require('eventsource');
    var request = require('request');
</code>
</pre>

<p><a href="https://nodejs.org/api/process.html#process_process_env"><code>process.env</code></a> lets you read environment variables, in this case a maximum number of clients, a token (for the POST request to the SSE server) and a URL for the server. </p>

<pre>
<code>    
    var maxClientCount = process.env.CLIENT_COUNT;
    var token = process.env.TOKEN;
    var satelliteUrl = process.env.SATELLITE_URL + "/broadcast";
</code>
</pre>

<p>Defining a class in ES6 is <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes">pretty straightforward</a>, writing a constructor method lets you initialise the object (<code>this.es</code> is the actual SSE client). </p>

<pre>
<code>
    class Client {
      constructor(url) {
        this.es = new EventSource(url);
      }
      receiveMessage(message) {
        return new Promise((resolve, reject) =&gt; {
          this.es.onmessage = (m =&gt; {if (m.data == message) resolve();});
        })
      }
      close() {
        this.es.close();
      }
    }

</code>
</pre>

<p>There are two things to note about <code>receiveMessage()</code>: one is the two <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions">arrow functions</a> (i.e., <code>((arg1, arg2) =&gt; { statement;})</code> instead of <code>function(arg1, arg2) { statement; }</code>) that I&rsquo;ve used, and the other is the <a href="https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promise</a> (<a href="http://javascriptplayground.com/blog/2015/02/promises/">read</a> <a href="http://www.html5rocks.com/en/tutorials/es6/promises/">more</a>) that it returns. The first arrow function (passed to the Promise object) is written this way so that the <code>this</code> in <code>this.es.onmessage</code> refers to the <code>this</code> of the class, while the second arrow function (assigned to <code>this.es.onmessage</code>) is just to keep things brief.</p>

<p>I used a promise so that I could do something when the client receives the sent message (or rather, when all the clients in the test do). A promise object takes a function that gets passed two functions as arguments, <code>resolve</code> and <code>reject</code>. </p>

<p>When things work out (in this case, one of the messages received by the eventsource object is the message we are interested in) call <code>resolve()</code> and the promise will resolve (and whatever you want to do next can take place). </p>

<p><code>reject</code> is a function you can call if things don&rsquo;t work out (and you want to pass on the error, for example). I&rsquo;m not using it because eventsource clients conveniently retry connections, so an error isn&rsquo;t really a failure. Skip to the end if you want to see how this promise (that we&rsquo;re returning) is used. </p>

<p>I need a random string to use as the channel name:</p>

<pre>
<code>
    var randomString = function() {
      var crypto = require('crypto')
      , shasum = crypto.createHash('sha1');
      shasum.update(Math.random().toString());
      return shasum.digest('hex');
    }
</code>
</pre>

<p>And a random number of clients to try sending a message to:</p>

<pre>
<code>
    var randomClientCount = function() {
      return getRandomInt(1, maxClientCount);
    }

    var getRandomInt = function(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    }
</code>
</pre>

<p>And here&rsquo;s the actual test, where I create a bunch of clients and store them in an array, before using <code>Promise.all</code> to log &ldquo;WORKS&rdquo; if all the clients receive the message. <code>Promise.all</code> takes an array of promises, and returns a promise that has a <code>then</code> method that takes two functions, the first one to be called if <code>Promise.all()</code> resolves and a second one that will be called if it doesn&rsquo;t (someone used <code>reject()</code>). </p>

<pre>
<code>
    var startTest = function () {
      var msg = 'PONG'
      var channelUrl = satelliteUrl+'/'+randomString();
      var clientCount = randomClientCount();
      var clients = [];
      
      for (let i=0; i&lt;clientCount; i++) {
        clients.push(new Client(channelUrl));
      }
      
      Promise.all(clients.map(c =&gt; c.receiveMessage(msg)))
      .then(function(response) {
        console.log("WORKS for "+clientCount+" clients");
        clients.map(c =&gt; c.close());
      }, function(error) {
        //this function won't be called, we haven't used reject anywhere
      });
     request.post(channelUrl).form({'token':token,'message':msg});
    }
</code>
</pre>

<p>As mentioned earlier, all this is to be able to log when all clients have received the message that is now going to be <a href="https://github.com/request/request#forms"><code>POST</code>&rsquo;d</a>. Now I just have to run <code>test()</code> <a href="https://nodejs.org/api/timers.html#timers_setinterval_callback_delay_arg">every second</a> or so. </p>

<pre>
<code>
    setInterval(function(){startTest()}, 1000);
</code>
</pre>