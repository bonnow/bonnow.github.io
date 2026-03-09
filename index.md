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


<div style="margin-bottom: 20px;">
  <input type="text" id="searchInput" placeholder="문서 제목 검색..." style="width: 100%; padding: 10px; font-size: 16px; border: 1px solid #ccc; border-radius: 4px;">
</div>

<div id="tagContainer" style="margin-bottom: 30px;">
  <button class="tag-btn" onclick="filterPosts('all')" style="cursor:pointer; padding:5px 10px; margin:2px;">전체보기</button>
  {% for tag in site.tags %}
    <button class="tag-btn" onclick="filterPosts('{{ tag[0] }}')" style="cursor:pointer; padding:5px 10px; margin:2px;">
      {{ tag[0] }} ({{ tag[1].size }})
    </button>
  {% endfor %}
</div>

<ul class="post-list" id="postList">
  {% for post in site.posts %}
    <li class="post-item" data-title="{{ post.title | downcase }}" data-tags="{{ post.tags | join: ',' }}">
      <span class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
      <h3>
        <a class="post-link" href="{{ post.url | relative_url }}">
          {{ post.title | escape }}
        </a>
      </h3>
    </li>
  {% endfor %}
</ul>

<script>
  // 1. 검색 기능
  document.getElementById('searchInput').addEventListener('keyup', function() {
    let query = this.value.toLowerCase();
    let posts = document.querySelectorAll('.post-item');
    
    posts.forEach(post => {
      let title = post.getAttribute('data-title');
      if (title.includes(query)) {
        post.style.display = '';
      } else {
        post.style.display = 'none';
      }
    });
  });

  // 2. 태그 필터링 기능
  function filterPosts(selectedTag) {
    let posts = document.querySelectorAll('.post-item');
    
    posts.forEach(post => {
      let tags = post.getAttribute('data-tags').split(',');
      if (selectedTag === 'all' || tags.includes(selectedTag)) {
        post.style.display = '';
      } else {
        post.style.display = 'none';
      }
    });
  }
</script>

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
