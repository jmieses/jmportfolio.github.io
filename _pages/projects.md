---
layout: archive
permalink: /projects/
title: "Project Posts by Tags"
author_profile: true
header:
 image: "images/alexandre-debieve-FO7JIlwjOtU-unsplash3.png"
---

{% for tag in site.tags %}
  <h2>{{ tag[0] }}</h2>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}