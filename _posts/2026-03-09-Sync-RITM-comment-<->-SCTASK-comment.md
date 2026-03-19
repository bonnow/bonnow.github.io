---
layout: post
title: "Sync RITM comment ↔ SCTASK comment"
date: 2026-03-09 10:00:00 +0800
categories: [servicenow platform]
tags: [requested item, catalog task, copy, comment, auto]
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

In ServiceNow, keeping communication synchronized between the **Requested Item (RITM)** and its associated **Catalog Tasks (SCTASK)** is essential for a smooth fulfillment process.

By default, comments made by an end-user on the RITM are not visible to the fulfiller on the Task, and vice versa. This post covers how to automate this synchronization using **Business Rules**.

<p class="lang-note"><strong>KR:</strong> RITM(요청자)과 SCTASK(실무자) 간의 커뮤니케이션을 원활하게 하기 위해, 한쪽에서 작성된 코멘트가 다른 쪽으로 자동 복사되도록 Business Rule을 설정하는 방법입니다.</p>

---

## 1. RITM comments → SCTASK comments
<img width="1376" height="788" alt="Untitled" src="https://github.com/user-attachments/assets/32a54121-85d1-4064-9802-cdae2afd036b" />
<img width="1375" height="413" alt="Untitled (1)" src="https://github.com/user-attachments/assets/2233c795-4cb3-41c0-9e5d-85df3d8997e9" />

This configuration ensures that any update provided by the requester on the RITM is instantly shared with the fulfillers working on the SCTASKs.

<p class="lang-note"><strong>KR:</strong> 요청자가 RITM에 남기는 코멘트를 하위의 모든 SCTASK로 자동 복사합니다.</p>

### [ Condition ]

To avoid unnecessary updates, we only trigger the rule when the person updating the record is the one for whom the request was made.

```jsx
current.requested_for == gs.getUserID()
```

### [ Script ]

```jsx
(function executeRule(current, previous /*null when async*/) {
    var chkTask = new GlideRecord('sc_task');
    chkTask.addQuery('request_item', current.getUniqueValue());
    chkTask.query();

    while (chkTask.next()) {
        chkTask.comments.setJournalEntry(current.comments);
        chkTask.update();
    }

})(current, previous);
```

---

## 2. SCTASK comments → RITM comments
<img width="1379" height="777" alt="Untitled (2)" src="https://github.com/user-attachments/assets/4f423572-4129-4017-a2dd-0f43c07ad279" />
<img width="1370" height="371" alt="Untitled (3)" src="https://github.com/user-attachments/assets/1ab13c34-af1b-4a3f-a53a-6762d660a081" />

When a fulfiller adds a comment to an SCTASK, it needs to be pushed up to the parent RITM so the end-user can see the progress.

<p class="lang-note"><strong>KR:</strong> Fulfiller가 SCTASK에 남기는 코멘트를 상위의 RITM로 자동 복사합니다.</p>

### [ Condition ]

```jsx
current.request_item.requested_for != gs.getUserID()
```

### [ Script ]

```jsx
(function executeRule(current, previous /*null when async*/) {

    var ritm = new GlideRecord("sc_req_item");
    ritm.addQuery("sys_id", current.request_item);
    ritm.query();

    if (ritm.next()) {
        ritm.comments = current.comments;
        ritm.update();
    }

})(current, previous);
```

---

## Important Notes

Always ensure your **Conditions** are strictly defined to prevent recursion. Without the `gs.getUserID()` check, the two Business Rules might keep triggering each other, creating an infinite loop of comments.

<p class="lang-note"><strong>KR:</strong> Warning: 무한 루프 방지를 위해 <code>gs.getUserID()</code>를 활용한 조건 설정이 필수입니다.</p>

</div>

<div class="lang-ko" markdown="1">

## 소개

ServiceNow에서는 **Requested Item (RITM)** 과 그에 연결된 **Catalog Task (SCTASK)** 간의 커뮤니케이션을 동기화하는 것이 원활한 요청 처리에 매우 중요합니다.

기본적으로 요청자가 RITM에 남긴 코멘트는 작업자(Fulfiller)가 보는 SCTASK에 바로 보이지 않고, 반대로 SCTASK에 남긴 코멘트도 요청자에게 바로 전달되지 않습니다. 이 글에서는 **Business Rule** 을 사용하여 이를 자동으로 동기화하는 방법을 설명합니다.

<p class="lang-note"><strong>EN:</strong> This post explains how to synchronize comments between RITM and SCTASK using Business Rules in ServiceNow.</p>

---

## 1. RITM comments → SCTASK comments

이 설정은 요청자가 RITM에 남긴 코멘트를 연결된 모든 SCTASK에 자동으로 복사하여, 작업자가 즉시 확인할 수 있도록 합니다.

<p class="lang-note"><strong>EN:</strong> Comments added by the requester on the RITM are automatically copied to all child SCTASK records.</p>

### [ Condition ]

불필요한 업데이트를 방지하기 위해, 레코드를 수정한 사용자가 요청 대상자(`requested_for`)인 경우에만 실행되도록 설정합니다.

```jsx
current.requested_for == gs.getUserID()
```

### [ Script ]

```jsx
(function executeRule(current, previous /*null when async*/) {
    var chkTask = new GlideRecord('sc_task');
    chkTask.addQuery('request_item', current.getUniqueValue());
    chkTask.query();

    while (chkTask.next()) {
        chkTask.comments.setJournalEntry(current.comments);
        chkTask.update();
    }

})(current, previous);
```

---

## 2. SCTASK comments → RITM comments

작업자(Fulfiller)가 SCTASK에 코멘트를 남기면, 그 내용을 상위 RITM으로 자동 복사하여 요청자도 진행 상황을 확인할 수 있도록 합니다.

<p class="lang-note"><strong>EN:</strong> Comments added by the fulfiller on the SCTASK are automatically copied back to the parent RITM.</p>

### [ Condition ]

```jsx
current.request_item.requested_for != gs.getUserID()
```

### [ Script ]

```jsx
(function executeRule(current, previous /*null when async*/) {

    var ritm = new GlideRecord("sc_req_item");
    ritm.addQuery("sys_id", current.request_item);
    ritm.query();

    if (ritm.next()) {
        ritm.comments = current.comments;
        ritm.update();
    }

})(current, previous);
```

---

## 중요 사항

무한 루프를 방지하려면 **Condition을 명확하게 설정해야 합니다.**  
`gs.getUserID()` 조건이 없으면 두 Business Rule이 서로를 계속 호출하면서 댓글이 반복 복사되는 문제가 발생할 수 있습니다.

<p class="lang-note"><strong>EN:</strong> The user check is important to prevent recursive updates between the two Business Rules.</p>

</div>
