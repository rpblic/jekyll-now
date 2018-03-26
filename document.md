---
layout: page
title: Docs
---

<div class="test">
  {% for post in site.categories %}
    <p>
      {{ post.title }}
    </p>
  {% endfor %}
</div>