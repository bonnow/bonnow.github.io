---
layout: post
title: "Service Request System Properties"
date: 2026-03-21 10:00:00 +0800
categories: [servicenow itsm]
tags: [request, workflow, flow, service]
---

<style>
  .lang-switcher {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
    margin-bottom: 30px;
    padding: 10px 0;
    border-bottom: 1px solid var(--main-border-color);
  }

  .lang-btn {
    padding: 8px 16px;
    border-radius: 999px;
    border: 1px solid var(--button-border-color);
    background-color: var(--card-bg);
    color: var(--text-color);
    font-size: 0.9rem;
    font-weight: 600;
    cursor: pointer;
    outline: none;
    transition: all 0.2s ease;
  }

  .lang-btn:hover {
    border-color: var(--link-color);
    transform: translateY(-1px);
  }

  .lang-btn.active {
    background-color: var(--link-color);
    color: #fff;
    border-color: var(--link-color);
  }

  .lang-ko {
    display: none;
  }

  .lang-note {
    color: #888;
    font-size: 0.9em;
  }
</style>

<div class="lang-switcher">
  <button class="lang-btn active" id="btn-en" onclick="switchLang('en')">🌐 English</button>
  <button class="lang-btn" id="btn-ko" onclick="switchLang('ko')">🇰🇷 한국어</button>
</div>

<script>
  function switchLang(lang) {
    const enElements = document.querySelectorAll('.lang-en');
    const koElements = document.querySelectorAll('.lang-ko');
    const btnEn = document.getElementById('btn-en');
    const btnKo = document.getElementById('btn-ko');

    if (lang === 'ko') {
      enElements.forEach(function(el) {
        el.style.display = 'none';
      });
      koElements.forEach(function(el) {
        el.style.display = 'block';
      });
      btnEn.classList.remove('active');
      btnKo.classList.add('active');
    } else {
      enElements.forEach(function(el) {
        el.style.display = 'block';
      });
      koElements.forEach(function(el) {
        el.style.display = 'none';
      });
      btnKo.classList.remove('active');
      btnEn.classList.add('active');
    }
  }
</script>

<div class="lang-en" markdown="1">
## This is the System properties that made for flow logic and to help managing Request Flow.

### Dynamic approval Group

![image](https://github.com/user-attachments/assets/12bd6686-f9ef-4a9b-b743-8623fb0f9d67#.png)


### Dynamic Approval On/Off

![image](https://github.com/user-attachments/assets/bc82c824-5602-49ba-9bec-ab48f6c43c5c#.png)


### Dynamic Approvl Role

![image](https://github.com/user-attachments/assets/47bcb513-fef0-48f0-84ae-d1ef4c5cd806#.png)


</div>

<div class="lang-ko" markdown="1">
## This is the System properties that made for flow logic and to help managing Request Flow.

### Dynamic approval Group

![image](https://github.com/user-attachments/assets/12bd6686-f9ef-4a9b-b743-8623fb0f9d67#.png)


### Dynamic Approval On/Off

![image](https://github.com/user-attachments/assets/bc82c824-5602-49ba-9bec-ab48f6c43c5c#.png)


### Dynamic Approvl Role

![image](https://github.com/user-attachments/assets/47bcb513-fef0-48f0-84ae-d1ef4c5cd806#.png)

</div>
