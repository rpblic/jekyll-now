---
layout: page
title: Docs
---

<div class="docs">
  {% for doc in paginator.docs %}
  <div class="doc">
    <h3 class="doc-title">
      <a href="{{ site.baseurl }}/{{ doc.url }}">
        {{ doc.title }}
      </a>
    </h3>

    <span class="doc-date">{{ doc.date | date_to_string }}</span>

    {{ doc.content }}
  </div>
  {% endfor %}
</div>

<div class="pagination">
  {% if paginator.next_page %}
    <a class="pagination-item older" href="{{ site.baseurl }}/page{{paginator.next_page}}">Older</a>
  {% else %}
    <span class="pagination-item older">Older</span>
  {% endif %}
  {% if paginator.previous_page %}
    {% if paginator.page == 2 %}
      <a class="pagination-item newer" href="{{ site.baseurl }}/">Newer</a>
    {% else %}
      <a class="pagination-item newer" href="{{ site.baseurl }}/page{{paginator.previous_page}}">Newer</a>
    {% endif %}
  {% else %}
    <span class="pagination-item newer">Newer</span>
  {% endif %}
</div>

