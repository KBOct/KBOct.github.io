---
layout: archive
permalink: /data-science/
title: "Data Science Posts"
author_profile: true
header:
  image: "/images/DS.jpg"
---

{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <!-- <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2> -->
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
