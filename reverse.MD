---
layout: page
title: Reversing
permalink: /reverse
---
{% for category in site.categories %}
  {% if category[0] == "reverse" %}
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
  {% endif %}
  {% endfor %}