---
layout: post
title: "Configure Dashboard which shows in Workspace"
date: 2026-03-10 10:00:00 +0800
categories: [servicenow platform]
tags: [workspace, dashboard, platform]
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

When you create a dashboard that needs to be displayed inside a Workspace, it must be created and configured from the correct location.

<p class="lang-note"><strong>KR:</strong> Workspace에 보여지는 dashboard를 구성할 때는 정해진 위치에서 생성하고 설정해야 합니다.</p>

---

## Where to configure the dashboard

To create or modify a dashboard that is shown in Workspace, navigate to:

**Platform Analytics > Library > Dashboards**

<p class="lang-note"><strong>KR:</strong> Workspace에 보여지는 dashboard를 만들거나 수정하려면 <code>Platform Analytics &gt; Library &gt; Dashboards</code> 로 이동해야 합니다.</p>

<img width="1118" height="880" alt="Annotation 2026-03-10 132251" src="https://github.com/user-attachments/assets/5424d6ea-739d-4700-a42c-71b04ee3896f" />

---

## Navigation path

Open the application navigator and go to:

`Platform Analytics > Library > Dashboards`

<p class="lang-note"><strong>KR:</strong> Application Navigator에서 <code>Platform Analytics &gt; Library &gt; Dashboards</code> 메뉴로 이동합니다.</p>

<img width="309" height="450" alt="Annotation 2026-03-11 102728" src="https://github.com/user-attachments/assets/d0631711-7c32-47bf-9fe8-773d1eea617c" />

<img width="1320" height="901" alt="Annotation 2026-03-11 102813" src="https://github.com/user-attachments/assets/70c86fe4-2f5c-4142-ade5-fa94b2a9de1c" />

---

## What you can do there

From this page, you can create a new dashboard or edit an existing dashboard that will be displayed in the Workspace.

<p class="lang-note"><strong>KR:</strong> 여기서 Workspace에 표시되는 dashboard를 새로 만들거나 기존 dashboard를 수정할 수 있습니다.</p>

---

## Summary

If the dashboard is intended for Workspace, do not configure it from a random dashboard location. Use the dashboard records managed under **Platform Analytics > Library > Dashboards**.

<p class="lang-note"><strong>KR:</strong> Workspace용 dashboard라면 다른 임의의 위치가 아니라 반드시 <code>Platform Analytics &gt; Library &gt; Dashboards</code> 에서 구성해야 합니다.</p>

</div>

<div class="lang-ko" markdown="1">

## 소개

Workspace 안에서 표시되는 dashboard를 만들려면, 반드시 올바른 위치에서 생성하고 설정해야 합니다.

<p class="lang-note"><strong>EN:</strong> Dashboards shown in Workspace should be created and configured from the correct location.</p>

---

## Dashboard를 어디서 설정해야 하는가

Workspace에 표시될 dashboard를 만들거나 수정하려면 아래 경로로 이동해야 합니다.

**Platform Analytics > Library > Dashboards**

<p class="lang-note"><strong>EN:</strong> To create or modify a dashboard shown in Workspace, go to <code>Platform Analytics &gt; Library &gt; Dashboards</code>.</p>

<img width="1118" height="880" alt="Annotation 2026-03-10 132251" src="https://github.com/user-attachments/assets/5424d6ea-739d-4700-a42c-71b04ee3896f" />

---

## 이동 경로

Application Navigator에서 아래 메뉴로 이동합니다.

`Platform Analytics > Library > Dashboards`

<p class="lang-note"><strong>EN:</strong> Open the application navigator and navigate to <code>Platform Analytics &gt; Library &gt; Dashboards</code>.</p>

<img width="309" height="450" alt="Annotation 2026-03-11 102728" src="https://github.com/user-attachments/assets/d0631711-7c32-47bf-9fe8-773d1eea617c" />

<img width="1320" height="901" alt="Annotation 2026-03-11 102813" src="https://github.com/user-attachments/assets/70c86fe4-2f5c-4142-ade5-fa94b2a9de1c" />

---

## 여기서 할 수 있는 것

이 화면에서 Workspace에 표시될 새 dashboard를 만들거나, 이미 존재하는 dashboard를 수정할 수 있습니다.

<p class="lang-note"><strong>EN:</strong> From this page, you can create a new dashboard or edit an existing dashboard that appears in Workspace.</p>

---

## 정리

Workspace에서 사용할 dashboard라면 아무 dashboard 메뉴에서 작업하지 말고, 반드시 **Platform Analytics > Library > Dashboards** 경로에서 구성해야 합니다.

<p class="lang-note"><strong>EN:</strong> If the dashboard is intended for Workspace, use <code>Platform Analytics &gt; Library &gt; Dashboards</code> rather than another dashboard location.</p>

</div>
