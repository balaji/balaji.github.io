---
layout: page
title: Tags
permalink: /tags/
---
<!-- <ul>
{% for tag in site.tags %}
  <li><a href="/tag/{{ tag | first }}.html">{{ tag | first }}</a>
  </li>
{% endfor %}
</ul> -->

<div id="tag-cloud">
  {{ site | tag_cloud }}
</div>
