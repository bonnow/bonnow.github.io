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

---

## About

이 블로그는 ServiceNow 실무에서 정리한 아키텍처, 구현 방법, 스크립트, 트러블슈팅 기록을 공유하는 공간입니다.  
This blog shares ServiceNow architecture notes, implementation ideas, scripts, and troubleshooting records.