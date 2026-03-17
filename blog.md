---
layout: default
title: Blog
permalink: /blog/
---

<div class="page-container">

  <h1 class="prose-page-title">Blog</h1>
  <p class="prose-page-subtitle">Articles and technical notes on causal inference, probabilistic modeling, and applied ML.</p>

  <div class="cards-grid" style="margin-top: 2rem;">
    {% for post in site.posts %}
    <a href="{{ post.url | relative_url }}" class="resource-card">
      <div class="card-meta">{{ post.date | date: "%B %-d, %Y" }}</div>
      <h2 class="card-title">{{ post.title }}</h2>
      {% if post.excerpt %}
      <p class="card-desc">{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
      {% endif %}
    </a>
    {% endfor %}
  </div>

</div>
