---
title: "UMC세종대"
layout: archive
permalink: categories/umc
author_profile: true
types: posts
---

{% assign posts = site.categories['umc']%}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %}
{% endfor %}
