---
layout: home
title: Home
nav_order: 1
---

# My Blog

개발과 배운 것들을 정리하는 GitHub Pages 블로그입니다.

## Latest Posts

{% assign recent_posts = site.posts | slice: 0, 5 %}
{% if recent_posts.size > 0 %}
{% for post in recent_posts %}
### [{{ post.title }}]({{ post.url | relative_url }})

{{ post.date | date: "%Y-%m-%d" }}

{{ post.excerpt | strip_html | truncatewords: 35 }}
{% endfor %}
{% else %}
아직 작성된 글이 없습니다.
{% endif %}

[전체 글 보기]({{ "/posts/" | relative_url }}){: .btn .btn-primary }
