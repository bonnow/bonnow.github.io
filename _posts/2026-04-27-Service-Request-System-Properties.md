---
layout: post
title: "Service Request System Properties"
date: 2026-04-27 10:00:00 +0800
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
## This is a System Property created for flow logic and to help manage Reuqest Management.

### Dynamic approval Group

Description : Group that shows the users whoo currently holding the role (which was set in dynamic_role_approval system property). Need to write the sys_id of the group.

![image](https://github.com/user-attachments/assets/12bd6686-f9ef-4a9b-b743-8623fb0f9d67#.png)


### Dynamic Approval On/Off

![image](https://github.com/user-attachments/assets/bc82c824-5602-49ba-9bec-ab48f6c43c5c#.png)


### Dynamic Approval Role

![image](https://github.com/user-attachments/assets/47bcb513-fef0-48f0-84ae-d1ef4c5cd806#.png)


### Assign Order

![image](https://github.com/user-attachments/assets/6fdf3968-78e5-4335-a1ae-360b726dd8d1#.png)


### Auto approve

![image](https://github.com/user-attachments/assets/de5671c7-0026-490f-a966-1cbd67949730#.png)


### Error Notification Recipients

![image](https://github.com/user-attachments/assets/14495f9e-2660-486c-be45-ae43e699a490#.png)


### Finish Ritm

![image](https://github.com/user-attachments/assets/bbef1fc4-ea82-4794-bfa0-ad2641f8380a#.png)


### Remove Duplicate Approve

![image](https://github.com/user-attachments/assets/0a3acb6b-1239-4340-a1e4-6564ce3b97d5#.png)


### Show Order

![image](https://github.com/user-attachments/assets/5c69791a-c31e-43e0-ac12-3706c46132a9#.png)


### System admin

![image](https://github.com/user-attachments/assets/c4a8fe3e-bb1a-4e3c-b31b-60bd5d70d490#.png)




</div>

<div class="lang-ko" markdown="1">
## 아래의 System Property는 Service Request Flow 로직을 구성하고 Request Management 관리를 위해 만들어졌습니다.

### Dynamic approval Group

![image](https://github.com/user-attachments/assets/12bd6686-f9ef-4a9b-b743-8623fb0f9d67#.png)


### Dynamic Approval On/Off

![image](https://github.com/user-attachments/assets/bc82c824-5602-49ba-9bec-ab48f6c43c5c#.png)


### Dynamic Approval Role

![image](https://github.com/user-attachments/assets/47bcb513-fef0-48f0-84ae-d1ef4c5cd806#.png)


### Assign Order

![image](https://github.com/user-attachments/assets/6fdf3968-78e5-4335-a1ae-360b726dd8d1#.png)


### Auto approve

![image](https://github.com/user-attachments/assets/de5671c7-0026-490f-a966-1cbd67949730#.png)


### Error Notification Recipients

![image](https://github.com/user-attachments/assets/14495f9e-2660-486c-be45-ae43e699a490#.png)


### Finish Ritm

![image](https://github.com/user-attachments/assets/bbef1fc4-ea82-4794-bfa0-ad2641f8380a#.png)


### Remove Duplicate Approve

![image](https://github.com/user-attachments/assets/0a3acb6b-1239-4340-a1e4-6564ce3b97d5#.png)


### Show Order

![image](https://github.com/user-attachments/assets/5c69791a-c31e-43e0-ac12-3706c46132a9#.png)


### System admin

![image](https://github.com/user-attachments/assets/c4a8fe3e-bb1a-4e3c-b31b-60bd5d70d490#.png)


</div>
