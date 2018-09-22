{% assign posts = site.pages | sort: 'date' | reverse %}
{% for post in posts %}- [{{post.title}}]({{post.url}}){% endfor %}
