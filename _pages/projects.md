---
layout: archive
permalink: /projects/
title: "Project Posts by Tags"
author_profile: true
header:
 image: "images/alexandre-debieve-FO7JIlwjOtU-unsplash3.png"
---

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>