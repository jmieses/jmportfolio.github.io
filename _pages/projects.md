---
layout: archive
permalink: /projects/
title: "Project Posts by Tags"
author_profile: true
header:
 image: "images/alexandre-debieve-FO7JIlwjOtU-unsplash3.png"
---

{% for post in site.posts %} {% include archive-single.html %} {% endfor %}
{% capture written_label %}'None'{% endcapture %}

{% for collection in site.collections %} {% unless collection.output == false or collection.label == "posts" %} {% capture label %}{{ collection.label }}{% endcapture %} {% if label != written_label %}