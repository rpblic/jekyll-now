---
layout: page
title: Docs
---

<div class="test">
  {% for post in site.categories.document %}
    <p>
      {{ post }}
    </p>
  {% endfor %}
</div>
