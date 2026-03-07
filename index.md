---
layout: default
title: Home
---

<div class="home-hero">
  <div class="hero-box">
    <p class="hero-eyebrow">SERVICE NOW BLOG</p>
    <h1>Bon's ServiceNow Notes</h1>
    <p class="hero-desc">
      ServiceNow architecture, implementation notes, scripts, troubleshooting, and real-world technical records.
    </p>
    <div class="hero-tags">
      <span>Discovery</span>
      <span>CMDB</span>
      <span>Asset / HAM</span>
      <span>Catalog</span>
      <span>Integration</span>
      <span>Troubleshooting</span>
    </div>
  </div>
</div>

## Latest Posts

<div class="post-list-grid">
{% for post in site.posts limit:9 %}
  <article class="post-card">
    <div class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</div>
    <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
    {% if post.excerpt %}
      <p>{{ post.excerpt | strip_html | truncate: 140 }}</p>
    {% endif %}
    <a class="read-more" href="{{ post.url | relative_url }}">Read more →</a>
  </article>
{% endfor %}
</div>