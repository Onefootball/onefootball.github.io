---
layout: default
title: 'Browse articles by tag'
permalink: /tag/
---

<h1 class="page-title">Browse articles by tag</h1>

<ul class="tag-list-main">
{% for tag in site.data.tags %}
    <li>
      <a href="/tag/{{tag.slug}}">
        #{{tag.name}}
      </a>
    </li>
{% endfor %}
</ul>
