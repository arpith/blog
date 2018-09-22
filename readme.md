# [arpith.co](https://arpith.co)

GitHub pages site for [arpith.co](https://arpith.co).

The posts were exported from Medium with [mediumexporter](https://github.com/xdamman/mediumexporter) and Tumblr with [tumblr2markdown](https://github.com/jaanus/tumblr2markdown). 

The post list / index is generated with the following liquid code:

```
{% assign posts = site.pages | sort: 'date' | reverse %}
{% for post in posts %}
- [{{post.title}}]({{post.url}}){% endfor %}
```

This lets me store the posts without a date in the filename.
