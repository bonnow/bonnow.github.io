---
layout: post
title: "Code Search"
date: 2026-03-24 10:00:00 +0800
categories: [servicenow platform]
tags: [script, search, code, find]
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

When we manage the ServiceNow, we sometimes suffer to find where is the code that we need to change.

---

## Example
<img width="212" height="42" alt="image" src="https://github.com/user-attachments/assets/23f266d5-cdf9-4cf4-ae6b-2ceba0d0e46d" />
For example someone who configured the system before, made a typo in the notification banner.

To find out where this error is.

Easiest way is using the ServiceNow studio.

<img width="322" height="423" alt="image" src="https://github.com/user-attachments/assets/9157a4e0-8d5a-463d-a8ff-f9bb8c9438a6" />
Search the Studio menu under the 'System Applications > Studio'.

<img width="916" height="855" alt="image" src="https://github.com/user-attachments/assets/7040b7c5-8f9a-4029-91e9-a152fa93d332" />
In the Studio click any scope that is not restricted.

In here, i clicked the 'ITSM Mobile Agent'.

<img width="2023" height="689" alt="image" src="https://github.com/user-attachments/assets/30c1c1a0-54bd-49c6-bd85-ae2233fe5263" />
Type **'Ctrl + Shift + F'** to see the search popup.

Type the sentence which included in the code that you want to search and click 'Search in all applications'.

<img width="1727" height="313" alt="image" src="https://github.com/user-attachments/assets/52881a63-c983-4f1b-b123-3399e726b7ef" />
<img width="1718" height="928" alt="image" src="https://github.com/user-attachments/assets/d3e949c4-3900-490d-989f-40e24d8b24b1" />

You can see the search result and where this typo sits in.

</div>

<div class="lang-ko" markdown="1">

## 소개

ServiceNow를 관리할떄, 특정 코드가 어디에 적혀있는지 찾는데 시간이 오래 걸리는 경우가 있습니다.

---

## Example
<img width="212" height="42" alt="image" src="https://github.com/user-attachments/assets/23f266d5-cdf9-4cf4-ae6b-2ceba0d0e46d" />
예를 들어서 과거의 누군가가 알림을 띄우는 것을 만들었는데, 해당 알림에 오타가 있어 고쳐야하는 경우

이 오류가 어디있는지 찾기 가장 쉬운 방법은, ServiceNow Studio를 활용하는 것입니다.

<img width="322" height="423" alt="image" src="https://github.com/user-attachments/assets/9157a4e0-8d5a-463d-a8ff-f9bb8c9438a6" />
'System Applications > Studio' 메뉴를 클릭합니다.

<img width="916" height="855" alt="image" src="https://github.com/user-attachments/assets/7040b7c5-8f9a-4029-91e9-a152fa93d332" />
Studio에서 접근이 제한되어 있지 않은 scope 아무거나 선택합니다.

여기서 저는 'ITSM Mobile Agent'를 선택했습니다.

<img width="2023" height="689" alt="image" src="https://github.com/user-attachments/assets/30c1c1a0-54bd-49c6-bd85-ae2233fe5263" />
**'Ctrl + Shift + F'**를 입력하여 검색창을 띄웁니다.

찾으려고 하는 코드에 들어있는 특정 문장을 입력하고 'Search in all applications'를 선택합니다.

<img width="1727" height="313" alt="image" src="https://github.com/user-attachments/assets/52881a63-c983-4f1b-b123-3399e726b7ef" />
<img width="1718" height="928" alt="image" src="https://github.com/user-attachments/assets/d3e949c4-3900-490d-989f-40e24d8b24b1" />

검색 결과로 해당 오타가 어디있었는지 쉽게 찾을 수 있습니다.
</div>
