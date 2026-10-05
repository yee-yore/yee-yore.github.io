---
layout: page
title: Red Team
icon: fas fa-user-secret
order: 1
---

{% assign posts = site.categories["Red Team"] %}
{% if posts %}
{% assign groups = posts | group_by_exp: "p", "p.categories[1] | default: 'Etc'" %}
{% for group in groups %}
### {{ group.name }}

{% for post in group.items %}
- [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

{% endfor %}
{% else %}
아직 작성된 글이 없습니다.
{% endif %}
