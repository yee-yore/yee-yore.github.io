---
layout: page
title: Threat Intelligence
icon: fas fa-magnifying-glass
order: 4
---

{% assign posts = site.categories["Threat Intelligence"] %}
{% if posts %}
{% for post in posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
{% else %}
아직 작성된 글이 없습니다.
{% endif %}
