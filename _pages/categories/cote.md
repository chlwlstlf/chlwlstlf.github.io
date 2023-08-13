---
title: "이코테"
layout: archive
permalink: categories/cote
author_profile: true
types: posts
---

{% assign posts = site.categories['cote']%}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %}
{% endfor %}
