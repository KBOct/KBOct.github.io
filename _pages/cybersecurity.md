---
layout: archive
permalink: /cybersecurity/
author_profile: true
title: "Cybersecurity Posts"
header:
  image: /images/cyber-security-mod.jpg
  caption: "Image by: [**TheDigitalArtist**](https://pixabay.com/users/TheDigitalArtist-202249/)"
---
{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
{% if category == 'cybersecurity'%}
  {% assign posts = group_items[forloop.index0] %}
  <!-- <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2> -->
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endif %}
{% endfor %}
