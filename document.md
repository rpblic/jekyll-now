---
layout: page
title: Docs
---

<div class="test">
  {% for post in site.categories %}
    <p>
      {{ post }}
    </p>
  {% endfor %}
</div>
