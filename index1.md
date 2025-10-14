---
layout: single
title: "Editorial Portfolio"
permalink: /
---

Welcome to my writing and scientific communication work. Below are representative samples.

{% assign items = site.posts | sort: "date" | reverse %}
{% for item in items %}
- [{{ item.title }}]({{ item.url }}) — <small>{{ item.date | date: "%b %d, %Y" }}</small><br/>
  {{ item.excerpt }}
{% endfor %}
