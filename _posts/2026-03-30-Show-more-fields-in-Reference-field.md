---
layout: post
title: "Show more fields in Reference field"
date: 2026-03-30 10:00:00 +0800
categories: [servicenow platform]
tags: [workspace, core, reference, fields, show, reveal]
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

When we input the reference field in workspace or core ui. System normally just show the ticket number, which is not really easy for user to input the data.

In this case, we can add short_description field, to show which ticket it is. To help users to input the correct data easily.

---

## Example

![image](https://github.com/user-attachments/assets/8e670625-00ee-45cf-8a7b-8280ae0a9c6b#.png)

If we want to put the parent field in Incident ticket, but if it's shows like this..

![image](https://github.com/user-attachments/assets/d3acb824-536d-47a9-aa1c-9e933948d82e#.png)

Go to the Dictionary entry for the field and Add the attribute.

```Javascript
ref_auto_completer=AJAXTableCompleter,ref_ac_columns=short_description,ref_ac_columns_search=true
```

Because, I typed the **short_description** for the _ref_ac_columns_.

![image](https://github.com/user-attachments/assets/56b17558-a912-47e0-b38a-a526c8a143c9#.png)

It will show the short_description field when you search the parent ticket.

</div>

<div class="lang-ko" markdown="1">

## 소개

Workspace나 Core UI에서 참조 필드를 입력할 떄, 시스템이 티켓 번호만 표시하면 사용자가 어떤 티켓인지 한눈에 파악하기 어려워 데이터를 정확히 입력하기가 쉽지 않습니다.

이런 경우, **short_description** 필드를 함께 표시하도록 설정하면 사용자가 원하는 데이터를 쉽게 찾아 입력할수 있도록 할 수 있습니다.

---

## 예시

![image](https://github.com/user-attachments/assets/8e670625-00ee-45cf-8a7b-8280ae0a9c6b#.png)

만약 인시던트 티켓의 Parent 필드를 입력하려고 하는데, 위와 같이 번호만 보인다면 관리가 어렵겠죠?

![image](https://github.com/user-attachments/assets/d3acb824-536d-47a9-aa1c-9e933948d82e#.png)

문제를 해결하려면, 해당 필드의 **Dictionary Entry**로 이동하여 아래의 **Attributes** 값을 추가해주면 됩니다.

```Javascript
ref_auto_completer=AJAXTableCompleter,ref_ac_columns=short_description,ref_ac_columns_search=true
```

위 설정에서 _ref_ac_columns_에 **short_description**을 입력하였기 때문에 이제 상위 티켓을 검색할 때 아래와 같이 short_description 필드도 같이 나타나게 됩니다.

![image](https://github.com/user-attachments/assets/56b17558-a912-47e0-b38a-a526c8a143c9#.png)

</div>
