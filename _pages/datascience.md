---
layout: archive
permalink: /data-science/
title: "Data Science Posts by Tags"
author_profile: true
header:
  image: "/images/DS.jpg"
---

<div id="archives">
<!-- {% for category in site.categories %} -->
   <div class="archive-group">
     {% capture category_name %}{{ category | first }}{% endcapture %}
     <div id="#{{ category_name | slugize }}"></div>
     <p></p>

     <h3 class="category-head">{{ data science }}</h3>
     <a name="{{ category_name | slugize }}"></a>
     {% for post in site.categories[data science] %}
     <article class="archive-item">
       <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
     </article>
     {% endfor %}
 </div>
<!-- {% endfor %} -->
</div>
