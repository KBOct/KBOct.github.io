---
layout: archive
permalink: /it/
title: "IT Posts"
author_profile: true
header:
  image: "/images/DS.jpg"
---

{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
{% if category == 'IT'%}
  {% assign posts = group_items[forloop.index0] %}
  <!-- <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2> -->
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endif %}
{% endfor %}
