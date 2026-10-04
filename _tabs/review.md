---
layout: page
title: Review
icon: fas fa-book-open
order: 5
---

{% for post in site.categories.Review %}
- [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
