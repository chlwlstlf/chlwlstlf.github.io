---
title: "C언어"
layout: archive
permalink: categories/c
author_profile: true
types: posts
---

{% assign posts = site.categories['c']%}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %}
{% endfor %}
