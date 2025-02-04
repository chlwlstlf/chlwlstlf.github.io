---
title: "소프티어"
layout: archive
permalink: categories/softeer
author_profile: true
types: posts
---

{% assign posts = site.categories['softeer']%}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %}
{% endfor %}
