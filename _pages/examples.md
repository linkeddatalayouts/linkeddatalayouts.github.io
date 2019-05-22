---
title: "Practical LiDL examples"
permalink: "/examples/"
layout: single
author_profile: false
classes: wide
---

{% for example in site.examples %}
  <div>
     <a href="{{ example.url }}">{{ example.title }}</a> 
   </div>
{% endfor %}

