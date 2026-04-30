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
This is a System Property created for flow logic and to help manage Reuqest Management.

### Dynamic approval Group

Group that shows the users whoo currently holding the role (which was set in dynamic_role_approval system property). Need to write the sys_id of the group.


![image](https://github.com/user-attachments/assets/12bd6686-f9ef-4a9b-b743-8623fb0f9d67#.png)



### Dynamic Approval On/Off

Property to turn on/off dynamic approval


![image](https://github.com/user-attachments/assets/bc82c824-5602-49ba-9bec-ab48f6c43c5c#.png)



### Dynamic Approval Role

Approval role that you want to manage dynamically. Write the sys_id of the role. Default value is 'business_stakeholder'.


![image](https://github.com/user-attachments/assets/47bcb513-fef0-48f0-84ae-d1ef4c5cd806#.png)



### Assign Order

task(or approval) assignment orders

- groups

- variables

- fields

only use, to separate the values. No spaces.

default : groups, variables, fields


![image](https://github.com/user-attachments/assets/6fdf3968-78e5-4335-a1ae-360b726dd8d1#.png)



### Auto approve

Automatically approve the approval task when the approver is the same person as the requester.

true : enable auto approval

false : disable auto approval


![image](https://github.com/user-attachments/assets/de5671c7-0026-490f-a966-1cbd67949730#.png)


### Error Notification Recipients

Email addresses when there is an Error at the SR flow or SR workflow


![image](https://github.com/user-attachments/assets/14495f9e-2660-486c-be45-ae43e699a490#.png)


### Finish Ritm

close RITM after finished all tasks 

default : true

If you set false, it will not close RITM automatically.


![image](https://github.com/user-attachments/assets/bbef1fc4-ea82-4794-bfa0-ad2641f8380a#.png)


### Remove Duplicate Approve

When the system requires approval from someone who has already approved the ticket, the new approval will be automatically granted.


![image](https://github.com/user-attachments/assets/0a3acb6b-1239-4340-a1e4-6564ce3b97d5#.png)


### Show Order

If this property is set as true, task order will be automatically written at ritm worknote.


![image](https://github.com/user-attachments/assets/5c69791a-c31e-43e0-ac12-3706c46132a9#.png)


### System admin

Put the main administrator of ServiceNow


![image](https://github.com/user-attachments/assets/c4a8fe3e-bb1a-4e3c-b31b-60bd5d70d490#.png)




</div>

<div class="lang-ko" markdown="1">
아래의 System Property는 Service Request Flow 로직을 구성하고 Request Management 관리를 위해 만들어졌습니다.

### Dynamic approval Group

Group that shows the users whoo currently holding the role (which was set in dynamic_role_approval system property). Need to write the sys_id of the group.


![image](https://github.com/user-attachments/assets/12bd6686-f9ef-4a9b-b743-8623fb0f9d67#.png)



### Dynamic Approval On/Off

Property to turn on/off dynamic approval


![image](https://github.com/user-attachments/assets/bc82c824-5602-49ba-9bec-ab48f6c43c5c#.png)



### Dynamic Approval Role

Approval role that you want to manage dynamically. Write the sys_id of the role. Default value is 'business_stakeholder'.


![image](https://github.com/user-attachments/assets/47bcb513-fef0-48f0-84ae-d1ef4c5cd806#.png)



### Assign Order

task(or approval) assignment orders

- groups

- variables

- fields

only use, to separate the values. No spaces.

default : groups, variables, fields


![image](https://github.com/user-attachments/assets/6fdf3968-78e5-4335-a1ae-360b726dd8d1#.png)



### Auto approve

Automatically approve the approval task when the approver is the same person as the requester.

true : enable auto approval

false : disable auto approval


![image](https://github.com/user-attachments/assets/de5671c7-0026-490f-a966-1cbd67949730#.png)


### Error Notification Recipients

Email addresses when there is an Error at the SR flow or SR workflow


![image](https://github.com/user-attachments/assets/14495f9e-2660-486c-be45-ae43e699a490#.png)


### Finish Ritm

close RITM after finished all tasks 

default : true

If you set false, it will not close RITM automatically.


![image](https://github.com/user-attachments/assets/bbef1fc4-ea82-4794-bfa0-ad2641f8380a#.png)


### Remove Duplicate Approve

When the system requires approval from someone who has already approved the ticket, the new approval will be automatically granted.


![image](https://github.com/user-attachments/assets/0a3acb6b-1239-4340-a1e4-6564ce3b97d5#.png)


### Show Order

If this property is set as true, task order will be automatically written at ritm worknote.


![image](https://github.com/user-attachments/assets/5c69791a-c31e-43e0-ac12-3706c46132a9#.png)


### System admin

Put the main administrator of ServiceNow


![image](https://github.com/user-attachments/assets/c4a8fe3e-bb1a-4e3c-b31b-60bd5d70d490#.png)



</div>
