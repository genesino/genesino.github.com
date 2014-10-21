---
title: Explain scripts
layout: page
---

<ul class="listing">
{% for cat in site.categories %}
{% if cat[0] == 'scripts' %}
{% for post in cat[1] %}
  <li class="listing-item">
  <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
  <a href="{{ site.baseurl }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
  </li>
{% endfor %}
{% endif %}
{% endfor %}
</ul>

