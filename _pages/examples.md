---
title: "Practical LiDL examples"
permalink: "/examples/"
layout: single
author_profile: false
---

{% for example in site.examples %}
  <div>
     <a href="{{ example.url }}">{{ example.title }}</a> 
   </div>
{% endfor %}

