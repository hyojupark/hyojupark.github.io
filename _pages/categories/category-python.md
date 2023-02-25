---
title: Python
layout: archive
permalink: /categories/python
---

{% assign posts = site.categories['Python'] %}
{% for post in posts %}
    {% include archive-single.html type=page.entries_layout %}
{% endfor %}