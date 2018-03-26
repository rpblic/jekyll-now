---
layout: page
title: Docs
---

<div class="test">
  {% for post in site.categories.Document %}
  <div class="post">
    <h5 class="post-title">
      <a href="{{ site.baseurl }}/{{ post.url }}">
        {{ post.title }}
      </a>
    </h5>
    <span class="post-date">{{ post.date | date_to_string }}</span>
  </div>
  {% endfor %}
</div>
