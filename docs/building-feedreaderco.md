---
layout: post
date: 2016-02-10 18:18:03 GMT
tags:
- feed reader
- reading
- rss
- atom
- feed
- API
- redis
- web development
- javascript
- nodejs
- express
- Amazon Web Services
- S3
- elasticache
- ec2
- load balancing
- ssl
- Heroku
- cloudflare
title: "Building Feedreader.co"
---
<p>The primary way I keep up with news around the internet is by keeping track of a bunch of blogs (the whole list is at <a href="https://feedreader.co/arpith%29.">https://feedreader.co/arpith)</a>. I do this with an RSS/Atom <a href="http://github.com/feedreaderco">feed reader</a> that I built, <a href="https://feedreader.co./">https://feedreader.co.</a>&nbsp;This is the story of how it works.<br></p><figure data-orig-width="800" data-orig-height="600" class="tmblr-full"><img src="/images/fa1a8c004087891d5805c655b7fd5c4780c2cffc845b9764c4bb539fdb9e5102.jpg" data-orig-width="800" data-orig-height="600"></figure><h2>Storing Feeds</h2><p>At its core, a feed reader has a list of feeds that it fetches and stores. Typically, a feed will have ten or twenty articles, so you’re going to have to store a list of articles (for when you want to infinite scroll all the way back). This is done using <a href="https://medium.com/u/8891d73b72be">Dan MacTough</a>’s excellent <a href="https://github.com/danmactough/node-feedparser">Feedparser module</a>. The articles are stored on <a href="https://aws.amazon.com/s3/">S3</a> using the <a href="http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html">AWS SDK</a> and the article ids are added to a <a href="http://redis.io/topics/data-types">sorted set</a> in <a href="http://redis.io/">Redis</a> (in <a href="https://aws.amazon.com/s3/">ElastiCache</a>).</p><h2>Organizing Feeds</h2><p>There are three things that Feedreader supports:</p><ol><li>Unread articles</li><li>Labeling articles</li><li>Grouping feeds into folders</li></ol><figure data-orig-width="800" data-orig-height="600" class="tmblr-full"><img src="/images/fc3008865616dfd3540e737f7ea9e63dbb4408fe27601823a9d74737f8e6cf90.jpg" data-orig-width="800" data-orig-height="600"></figure><p>Folders are stored as sets of feed ids (the feed url). Creating a feed for a folder is easy — once you’ve got the feed ids, Redis’s <a href="http://redis.io/commands/zunionstore">zunionstore</a> will give you a union of the sorted sets (of article ids).</p><p>Labels are similar to feeds — they are sorted sets of article ids. When articles are read, they are added to the label “read”. This makes it easy to fetch a list of articles that are unread:</p><p>First zunionstore is used to combine the feeds as well as the “read” label (which is assigned a negative weight). <a href="http://redis.io/commands/zrangebyscore">zrangebyscore</a> is then used to filter out the read articles.</p><h2>Working with feeds</h2><p>What’s the bigger picture? I’m glad you asked!</p><figure data-orig-width="800" data-orig-height="600" class="tmblr-full"><img src="/images/c7389ab6a3de7ff91950f15c6b456fd669df54cfff81b6472b98cc11541ebec9.jpg" data-orig-width="800" data-orig-height="600"></figure><h2>Feedreader API</h2><p>I’ve built a <a href="http://api.feedreader.co/">REST based API</a> that lets you fetch, add and organize your feeds.<a href="http://github.com/feedreaderco/api">This</a> is an <a href="http://expressjs.com/">Express</a> app running on <a href="https://aws.amazon.com/ec2/">EC2</a> behind a <a href="https://aws.amazon.com/elasticloadbalancing/">load balancer</a> (which lets me use <a href="https://aws.amazon.com/certificate-manager/">AWS Certificate Manager</a>). As mentioned earlier, it uses Redis (in ElastiCache) as a backend and stores articles as static files on S3.</p><h2>Bookmarklet</h2><p>You can <a href="https://feedreader.co/help/">subscribe to feeds</a> by using the <a href="https://feedreader.co/bookmarklet">bookmarklet</a> when you’re on a website you like. The bookmarklet <a href="https://feedreader.co/bookmarklet/js">looks through</a> the webpage you’re on to find the RSS/Atom feed url, and takes you to the Feedreader page for that feed.</p><h2>Feedreader.co</h2><p>This brings us to the actual <a href="http://feedreader.co/">website</a>. It’s a tiny Express app that runs on&nbsp;<a href="http://heroku.com/">Heroku</a> behind <a href="https://www.cloudflare.com/">CloudFlare</a>. All it does is serve a HTML file that fetches and presents data from the API. You can do fun things like adding feeds to different folders (yupp, a feed can be in more than one folder!) or add labels to articles. And there’s infinite scroll :)</p><p><a href="http://feedreader.co/">Sign up</a> if this sounds interesting!</p>