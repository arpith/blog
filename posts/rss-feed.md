---
layout: post
date: 2020-11-26 12:00:00 PT
title: "Creating an RSS feed"
---
# Creating an RSS feed
This blog is built with Jekyll on GitHub Pages and I just added an RSS feed to it. Here's how it's set up!

I created a file called `feed.xml` in the root directory with the following contents. The first few lines tells Jekyll not to apply any layout to it.
```
---
layout: none
---
```

Then some boiler plate to actually set up the feed.
```
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
	<channel>
		<title>Arpith Siromoney</title>
		<link rel="alternate" type="text/html" href="https://arpith.co/"/>
		<link rel="self" type="application/atom+xml" href="https://arpith.co/feed"/>
		<id>https://arpith.co/feed</id>
```

Finally the juicy bits! This is pretty similar to how the [homepage](https://raw.githubusercontent.com/arpith/blog/master/index.md) is set up, replacing markdown syntax with xml and actually including the text of the posts.
{% raw %}

```
{% assign posts = site.pages | sort: 'date' | reverse %}
{% for post in posts %}
<item>
		<title>{{ post.title | xml_escape }}</title>
		<description>{{ post.content | markdownify | xml_escape }}</description>
		<pubDate>{{ post.date | date: "%a, %d %b %Y %H:%M:%S %z" }}</pubDate>
		<link>https://arpith.co{{ post.url | escape }}</link>
		<guid isPermaLink="true">https://arpith.co{{ post.url }}</guid>
</item>
{% endfor %}
```
{% endraw %}

One thing that I learned was that guids should actually be unique or you run the risk of collisions in aggregators.

Don't forget to close all your tags!
```

	</channel>
</rss>

```
