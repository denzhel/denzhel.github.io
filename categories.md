---
layout: page
permalink: /categories/
title: Categories
---

{% for post in site.categories %}
 <li><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}