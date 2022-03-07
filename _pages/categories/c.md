---
layout: archive
permalink: c
title: "C"

author_profile: true
sidebar:
  nav: "docs"
---

{% assign posts = site.categories.c %}
{% for post in posts %}
  {% include custom-archive-single.html type=entries_layout %}
{% endfor %}