---
layout: page
title: Docs
---

<div class="posts">
  <ul>
    {% for post in site.categories.Document %}
      <div class="post">
        <li>
          <p>
            <a href="{{ site.baseurl }}/{{ post.url }}">
              {{ post.title }}
            </a>
            _{{ post.date | date_to_string }}
          </p>
          <p>
            {% if post.excerpt %}
              {{ post.excerpt | truncate: 80, '...' | strip_html | strip_newlines }}
            {% endif %}
          </p>
        </li>
        <hr>
      </div>
    {% endfor %}
  </ul>
</div>
