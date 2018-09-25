---
layout: post
date: 2018-09-23 21:48:00 ET
title: "New blog setup"
---

# New blog setup
I had been writing on Medium and Tumblr in the past but had always wanted to switch to a static website which I could have more control / ownership. That day has come! This post is an explanation of the new setup.

## Exporting my posts
I used [mediumexporter](https://github.com/xdamman/mediumexporter) to export my recent posts from Medium and [tumblr2markdown](https://github.com/jaanus/tumblr2markdown) to export the rest of the posts from Tumblr. I now had a directory of markdown files.

## GitHub pages
GitHub pages is a neat way to set up a static website. It uses Jekyll and renders markdown files without needing any extra configuration.

## Routes
I wanted urls to look like `arpith.co/new-blog-setup` but also wanted my posts to sit in a separate directory in the repository. To do this I used [this _config.yml](https://github.com/jekyll/jekyll/issues/5628#issuecomment-275459907), changing the "mypages" path to "posts". This let me drop "posts" from the urls.

```
defaults:
  -
    scope:
      path: "posts"
      type: "pages"
    values:
      permalink: "/:basename"
```

## Index page
My homepage is just a list of posts. This is a throwback to my undergrad days (before I switched to Tumblr). The post list is generated with the following Liquid code. I'm using pages instead of posts because I don't want to keep timestamps in my filenames. This means I need to sort `site.pages` by date and reverse it.

{% raw %}
```
{% assign posts = site.pages | sort: 'date' | reverse %}
{% for post in posts %}
- [{{post.title}}]({{post.url}}){% endfor %}
```
{% endraw %}

## Misc
I'm using `index.md` for the index page and `README.md` for the repository readme.

`assets/css/style.css` is where the style sheet lives and `_layouts` contains the templates. 

Finally, I set up my domain name to point to GitHub pages' addresses, and added the domain name in the repository's settings page. This created a file in the repository called `CNAME` which contains the domain name.

And that's it! I'm pretty happy with the result!

Thanks to [Alicja Raszkowska](https://trueskawka.github.io/) and [Lindsey Kuper](http://composition.al)!
