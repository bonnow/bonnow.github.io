---
layout: post
title: "Config-Order-Guide-to-be-attached-in-existing-UR-ticket"
date: 2026-05-15 10:00:00 +0800
categories: [servicenow itsm]
tags: [request, order guide, business rule, ui action]
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
When you submit order guide in existing UR ticket at SOW(Service Operations Workspace).
 + If your catalogue item set as UR certified.

 You will encounter that System is creating another UR ticket.

 In this case, if you set the notification to the requested_for user.

 The user will 


## Event to send the email

Create the event name 'send_email' to use send email with the event type record.

![image](https://github.com/user-attachments/assets/aa4b12d7-c48e-4b24-ab90-15e59043a95d#.png)





</div>

<div class="lang-ko" markdown="1">
SOW(Service Operations Workspace)에서 end-user가 생성한 UR에서 'create request' 버튼을 활용하여 order guide를 신청하려고 한다면.
+ catalog item이 UR certified가 check 되어 있는 경우

catalog를 만드는것과 달리 order guide를 제출하면 또다른 Universal Request ticket을 만들고 거기에 Requested Item 티켓들을 붙이는 경우가 생긴다.

이렇게 되면 대게 comment를 통해서 end-user에게 해당 티켓을 닫고 해당 내용은 새로 생성된 ticket에서 추적/관리 하겠다고 남긴뒤, user가 생성된 ticket을 닫는다.

하지만 이렇게 되면 user는 매번 관련 티켓을 생성할 때마다 티켓이 다른 티켓으로 이관되는 일을 겪게 되므로 UX 입장에서 보면 썩 좋은 경험은 아니라고 할 수 있다.

그렇다면 좋은 UX는 무엇일까? 통상적으로 생각할 때, order guide를 제출해도 다른 catalog를 제출할때와 마찬가지로 동작해야할 것이다.

이것을 위해서는 몇가지 config만 한다면 order guide도 catalog를 제출할 때 처럼 동작하게 할 수 있다.

## Idea

구현 포인트는 바로, ServiceNow가 REQ와 RITM을 생성하고 UR을 생성하여 mapping 시키기 전에 현재 UR의 sys_id를 맵핑시켜서 생성된 UR에 REQ와 RITM을 붙이는 것이다.

즉, OOB 프로세스 중간에 hijacking 해서 다른 길로 가게 만드는 것이다.


구현시에는 아래와 같은 기능들을 config 할 것이다.

- UI Action : 현재 UR의 sys_id를 서버에 넘겨주는 역할
- Script Include : 서버에 있는 값을 조회하기 위한 용도
- Business Rule : 새로운 UR을 만들기 전 가로채는 역할
- Variable set : 맵핑 되어야할 UR의 sys_id를 담아두는 용도




## UI Action
SOW에 있는 버튼인 'Create Request' 버튼을 조금 손봐야한다.

![image](https://github.com/user-attachments/assets/95749b61-9945-4459-9a80-62ea979174d3#.png)





## Event to send the email

Create the event name 'send_email' to use send email with the event type record.

![image](https://github.com/user-attachments/assets/aa4b12d7-c48e-4b24-ab90-15e59043a95d#.png)

</div>
