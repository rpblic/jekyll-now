---
layout: page
title: Docs
---

<div class="posts">
  {% for post in site.docs %}
  <div class="post">
    <h3 class="post-title">
      <a href="{{ site.baseurl }}/{{ post.url }}">
        {{ post.title }}
      </a>
    </h3>

    <span class="post-date">{{ post.date | date_to_string }}</span>

    {{ post.content }}
  </div>
  {% endfor %}
</div>

