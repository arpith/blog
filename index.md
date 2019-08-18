{% assign posts = site.pages | sort: 'date' | reverse %}
{% assign months = site.pages | sort: 'date' | reverse | map : 'date' | date: '%Y/%m' | uniq %}
{% for post in posts %}
- [{{post.title}}]({{post.url}}){% endfor %}

<ul>
{% for month in months %}
<li><a href="/{{ month }}">{{ month }}</a></li>
{% endfor %}
</ul>
