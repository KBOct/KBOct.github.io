---
layout: archive
permalink: /quant-finance/
title: "Quant Finance Posts by Tags"
author_profile: true
header:
  image: "/images/QUANT1.jpg"
---

{% for category in site.categories %}
    {% for post in category[1] %}
    {% assign posts = group_items[forloop.index0] %}
    <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
      {% for post in posts %}
        {% include archive-single.html %}
        {% endfor %}
    {% endfor %}
{% endfor %}
