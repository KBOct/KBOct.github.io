---
layout: archive
permalink: /quant-finance/
title: "Quantitative Finance Posts"
author_profile: true
header:
  image: "/images/QUANT1.jpg"
---
{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
{% if category == 'quantitative finance'%}
  {% assign posts = group_items[forloop.index0] %}
  <!-- <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2> -->
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endif %}
{% endfor %}
