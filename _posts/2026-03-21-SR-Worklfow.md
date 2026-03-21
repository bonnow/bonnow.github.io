---
layout: post
title: "Service Request Framework"
date: 2026-03-21 10:00:00 +0800
categories: [servicenow itsm, korean]
tags: [request, workflow, flow, framework, korean]
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
# Details of the SR workflow.

<img width="1966" height="1365" alt="Screenshot 2026-03-21 at 14 36 23" src="https://github.com/user-attachments/assets/f8d778ca-e45f-407c-8c6e-f693a384e91a" />
## Properties
<img width="1500" height="229" alt="image" src="https://github.com/user-attachments/assets/5197799b-77e2-43f4-a0ad-e80b613c2cc5" />
<img width="1498" height="100" alt="image" src="https://github.com/user-attachments/assets/fa087e22-33d4-421b-89ea-ca8dbd93a519" />
<img width="1500" height="88" alt="image" src="https://github.com/user-attachments/assets/8be28be2-1781-44e4-944d-cec8920e2b11" />
<img width="1499" height="182" alt="image" src="https://github.com/user-attachments/assets/11f6817b-a3bd-40cf-9216-79e90a42cc7c" />
<img width="1499" height="139" alt="image" src="https://github.com/user-attachments/assets/07ac4438-6852-407f-87bc-421546ce2b87" />
<img width="1501" height="535" alt="image" src="https://github.com/user-attachments/assets/b8e5bc12-2a31-4808-b3cf-fd98beda50ec" />
<img width="1501" height="260" alt="image" src="https://github.com/user-attachments/assets/eb2e6c80-1318-4420-aa1d-f4aefc9b74f9" />



</div>
<div class="lang-ko" markdown="1">
# SR workflow 세부 내용

<img width="1966" height="1365" alt="Screenshot 2026-03-21 at 14 36 23" src="https://github.com/user-attachments/assets/f8d778ca-e45f-407c-8c6e-f693a384e91a" />
## Properties
<img width="1500" height="229" alt="image" src="https://github.com/user-attachments/assets/5197799b-77e2-43f4-a0ad-e80b613c2cc5" />
<img width="1498" height="100" alt="image" src="https://github.com/user-attachments/assets/fa087e22-33d4-421b-89ea-ca8dbd93a519" />
<img width="1500" height="88" alt="image" src="https://github.com/user-attachments/assets/8be28be2-1781-44e4-944d-cec8920e2b11" />
<img width="1499" height="182" alt="image" src="https://github.com/user-attachments/assets/11f6817b-a3bd-40cf-9216-79e90a42cc7c" />
<img width="1499" height="139" alt="image" src="https://github.com/user-attachments/assets/07ac4438-6852-407f-87bc-421546ce2b87" />
<img width="1501" height="535" alt="image" src="https://github.com/user-attachments/assets/b8e5bc12-2a31-4808-b3cf-fd98beda50ec" />
<img width="1501" height="260" alt="image" src="https://github.com/user-attachments/assets/eb2e6c80-1318-4420-aa1d-f4aefc9b74f9" />


</div>
