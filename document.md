---
layout: page
title: Docs
---

<div class="test">
  {% for post in site.categories.document %}
    <p>
      {{ post.title }}
    </p>
  {% endfor %}
</div>
