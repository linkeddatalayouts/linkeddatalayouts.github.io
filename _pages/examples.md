---
layout: page
title: "A list of LiDL examples"
permalink: "/examples/"
---

<ul>
  {% for example in site.examples %}
    <li>
      <a href="{{ example.url }}">{{ example.title }}</a>
      - {{ example.headline }}
    </li>
  {% endfor %}
</ul>
