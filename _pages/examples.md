---
layout: page
title: "A list of LiDL examples"
permalink: "/examples/"
---

{% for example in site.examples %}
  <div>
     <a href="{{ example.url }}">{{ example.title }}</a> - {{ example.title }}
     {{ example.content }}
   </div>
{% endfor %}

