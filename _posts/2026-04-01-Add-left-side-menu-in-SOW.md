---
layout: post
title: "Add left side menu in SOW"
date: 2026-03-30 10:00:00 +0800
categories: [servicenow platform]
tags: [workspace, add menu, ux page]
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

## Introduction

![image](https://github.com/user-attachments/assets/e54a61b6-abfc-4620-8693-3dc3306b57da#.png)

Sometimes we want to add the menu at the left side menu of the Service Operations Workspace.

---

## How to?

1. Go to the **'UX Page Property(sys_ux_page_property)'** table list.


![image](https://github.com/user-attachments/assets/b229301d-30a3-4812-9767-680ae24dec57#.png)

2. Search the record named **chrome_toolbar**

3. Select the one with **Page : Service Operations Workspace** + **Application : Service Operations Workspace Core**


![image](https://github.com/user-attachments/assets/ec6f1fcb-8539-49fb-9b3f-29fc4fc07fdf#.png)

4. If you change the value, you can see your new menu in the SOW.

</div>

<div class="lang-ko" markdown="1">
  
## 소개

![image](https://github.com/user-attachments/assets/e54a61b6-abfc-4620-8693-3dc3306b57da#.png)

간혹, Service Operations Workspace 좌측 메뉴에 새로운 메뉴를 추가해야하는 경우가 있습니다.

---

## How to?

1. **'UX Page Property(sys_ux_page_property)'** table 리스트 페이지로 갑니다.


![image](https://github.com/user-attachments/assets/b229301d-30a3-4812-9767-680ae24dec57#.png)

2. 이름이 **chrome_toolbar**, **Page : Service Operations Workspace**, **Application : Service Operations Workspace Core** 인 레코드를 찾습니다.


![image](https://github.com/user-attachments/assets/ec6f1fcb-8539-49fb-9b3f-29fc4fc07fdf#.png)

3. 해당 레코드에 있는 value값을 수정해주면 새로운 메뉴가 SOW 좌측 리스트에 보이게 됩니다.

</div>
