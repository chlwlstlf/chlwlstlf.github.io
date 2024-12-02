---
title: "Next.js"
layout: archive
permalink: categories/next
author_profile: true
types: posts
---

{% assign posts = site.categories['next']%}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %}
{% endfor %}
