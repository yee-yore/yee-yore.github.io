---
layout: page
title: Blue Team
icon: fas fa-shield-halved
order: 3
---

{% assign posts = site.categories["Blue Team"] %}
{% if posts %}
{% for post in posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
{% else %}
아직 작성된 글이 없습니다.
{% endif %}
