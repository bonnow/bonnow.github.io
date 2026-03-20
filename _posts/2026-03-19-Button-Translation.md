---
layout: post
title: "Button Translation"
date: 2026-03-19 10:00:00 +0800
categories: [servicenow platform]
tags: [button, ui action, translation]
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

When you want to change the translation of the UI Action's translation.

---

## System Localization > Translated Name / Fields
<img width="1387" height="324" alt="Untitled" src="https://github.com/user-attachments/assets/e81f1741-5acf-483b-93bd-8a66625dd472" />

Create or edit the record in this menu.<br/>
⛳️ When updating an existing UI Action name rather than creating a new one, you must modify the 'Translated Name / Field' and then deactivate and reactivate the UI Action for the changes to take effect.

</div>

<div class="lang-ko" markdown="1">

## 소개

UI Action의 번역을 고치거나 새로 추가하고 싶은 경우.

---

## System Localization > Translated Name / Fields
<img width="1387" height="324" alt="Untitled" src="https://github.com/user-attachments/assets/e81f1741-5acf-483b-93bd-8a66625dd472" />

여기 메뉴에서 레코드를 새로 생성하거나 변경하면 됩니다.<br/>

⛳️ 만약 해당 ui action의 명칭을 새로 생성하는게 아니라 기생성된 명칭을 변경할 때는 ‘translated name / field’ 변경 후에 해당 ui action을 inactive 했다가 다시 active 시켜야 반영됩니다.
</div>
