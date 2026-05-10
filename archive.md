---
layout: page
title: "Archive"
permalink: /archive/
---

{% if site.posts.size > 0 %}
<div class="archive-list">
  {% for post in site.posts %}
    <article class="archive-item">
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y-%m-%d" }}</time>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </article>
  {% endfor %}
</div>
{% else %}
<p>还没有文章。</p>
{% endif %}
