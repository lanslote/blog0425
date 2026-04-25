---
title: Posts
nav_order: 2
permalink: /posts/
---

# Posts

{% if site.posts.size > 0 %}
{% for post in site.posts %}
## [{{ post.title }}]({{ post.url | relative_url }})

{{ post.date | date: "%Y-%m-%d" }}

{{ post.excerpt | strip_html | truncatewords: 45 }}
{% endfor %}
{% else %}
아직 작성된 글이 없습니다.
{% endif %}
