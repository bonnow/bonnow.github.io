---
layout: post
title: "Config Order Guide to be attached in existing UR ticket"
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
When Processing a Universal Request(UR) created by an end-user in the Service Operations Workspace(SOW), agents often use the 'Create Request' bvutton to submit an Order Guide.

While standard Catalog Items with 'UR Certified' checked integrate seamlessly with the existing UR, submitting an Order Guide triggers the standard OOB(Out of the Box) process tho create a brand-new UR ticket, subsequently attaching the newly generated Requested Items(RITMs) to it.

Consequently, current operational workarounds require inefficient manual steps:

1. To agent leaves a commetn asking the end-user to "Closing the current ticket and track progress in the newly created one".

2. The agent will close the original ticket.

From a UX perspective, forcing users to experience ticket transfers every time they request services leads to a poor and disjointed journey.

Therefore, the ideal UX should ensure that even when an Order Guide is submitted, it behaves exactly like a standard Catalog Item - generating the REQ and RITMs under the existing UR without creating duplicates.

With a few configuration adjustments, this seamless behavior can be achieved.



## Idea & Architecture

The core solution involves hijacking the OOB process before ServiceNow creates a new UR and maps the REQ/RITM.

By capturing the sys_id of the current UR, we can forcefully mpa the newly created REQ and RITMs back to the original UR.

To implement this, we configure the following four components:

- UI Action : Saves the current UR's sys_id to the server session and opens the catalog.

- Script Include : Acts as a utility to store and retrieve session data on the server side.

- Business Rule : Intercepts the process right before a new UR is automatically generated, linking the REQ to the existing UR instead.

- Variable Set : Stores the target UR's sys_id at the RITM level for streamlined management.



## UI Action

![image](https://github.com/user-attachments/assets/95749b61-9945-4459-9a80-62ea979174d3#.png)

We need to modify the behavior of the 'Create Request' button in SOW.

![image](https://github.com/user-attachments/assets/9b6be38b-1df7-43ce-b41e-0ce0b0095ede#.png)

Find the 'Create Request' button in the sys_ui_action table. (There are two UI Actions with this name - one for Core UI and one for Workspace. Ensure you select the Workspace UI Action because in this sample we will fix the button only in the SOW).


```javascript
function onClick() {
    var ga = new GlideAjax('sn_uni_req.UniversalRequestAJAXUtils');
    ga.addParam('sysparm_name', 'hasPrimaryTicket');
    ga.addParam('sysparm_ur_sys_id', g_form.getUniqueValue());
    ga.getXMLAnswer(function(answer) {
        if (answer == "true")
            g_form.addErrorMessage(getMessage("You cannot create a new request. The Universal Request already has a primary ticket associated with it."));
        else {
            var saveGa = new GlideAjax('URSessionUtil');
            saveGa.addParam('sysparm_name', 'saveUrId');
            saveGa.addParam('sysparm_ur_id', g_form.getUniqueValue());
            saveGa.getXMLAnswer(function(response) {
                var params = {};
                params.sysparm_parent_table = g_form.getTableName();
                params.sysparm_parent_sys_id = g_form.getUniqueValue();
                g_service_catalog.openCatalogItem('sc_cat_item', '-1', params);
            });
        }
    });
}
```

This modification ensures that the moment a user clicks the button, the sys_id of the currently open UR is securely stored in the sever session.



## Script Include

Navigate to 'System Definition > Script Includes' to create a server-side script for managing session data.

![image](https://github.com/user-attachments/assets/b007cfb6-70b8-417b-90b5-734ed4207b0e#.png)

```javascript
var URSessionUtil = Class.create();
URSessionUtil.prototype = Object.extendsObject(AbstractAjaxProcessor, {
    saveUrId: function() {
        var urId = this.getParameter('sysparm_ur_id');
        gs.getSession().putClientData('source_ur_id', urId);
        return urId;
    },

    getUrId: function() {
        return gs.getSession().getClientData('source_ur_id');
    },

    type: 'URSessionUtil'
});
```



## Business Rule

Create a Business rule to intercept the OOB logic that generates a duplicate UR upon REQ creation, and force-map the REQ to the existing UR stored in the session.

![image](https://github.com/user-attachments/assets/cb95c5e7-8ab1-41b1-982e-39781540934d#.png)

Lowered the order number to 10. To execute before standard system rules.

```javascript
(function executeRule(current, previous /*null when async*/) {
    var existingUrId = gs.getSession().getClientData('source_ur_id');
    
    gs.log("DEBUG: Session UR ID is " + existingUrId, "UR_LINK");

    if (existingUrId) {
        current.universal_request = existingUrId;

        try {
            new sn_uni_req.UniversalRequestUtils().attachPrimaryTicket(existingUrId, current.sys_id, null, true);
        } catch (e) {
            gs.error("UR Link Error: " + e.getMessage());
        }

        gs.getSession().putClientData('source_ur_id', '');
    }
})(current, previous);
```


## Variable Set

To seamlessly pass and maintain the sys_id at each RITM level, create a reusable Variable Set containing a variable named parent_sys_id.

![image](https://github.com/user-attachments/assets/6f64093e-e15a-4d57-8e58-0e709f1d79a4#.png)

![image](https://github.com/user-attachments/assets/c32644fc-a90d-4096-8be7-2a9e8f4a82df#.png)

![image](https://github.com/user-attachments/assets/d0f87966-e863-486b-a788-e8553e565aed#.png)

```javascript
function onLoad() {
    var ga = new GlideAjax('URSessionUtil');
    ga.addParam('sysparm_name', 'getUrId');
    ga.getXMLAnswer(function(answer) {
        if (answer) {
            g_form.setValue('parent_sys_id', answer); 
        }
    });
}
```






</div>

<div class="lang-ko" markdown="1">
Service Operations Workspace(SOW)에서 End-user가 생성한 Universal Request(UR)를 처리할 때, 'Create Request' 버튼을 통해 Order Guide를 제출하는 경우가 있습니다.

일반 Catalog Item의 경우 'UR Certified'가 체크되어 있어도 기존 UR 티켓과 정상적으로 연동되지만, Order Guide를 제출하면 OOB(Out of the Box) 프로세스에 따라 새로운 UR 티켓이 추가로 생성되고 그 하위에 Requested Item(RITM) 티켓들이 매핑되는 문제가 발생합니다.

이로 인해 대다수의 업무 프로세스에서는 다음과 같은 비효율적인 우회 방식을 사용하고 있습니다.

1. Employee가 comment를 통해서 End-user에게 *"기존 티켓을 닫고, 새로 생성된 티켓에서 진행 상황을 추적해 달라"*고 안내합니다.

2. Employee가 티켓을 종료시킵니다.

이는 유저 입장에서 매번 티켓이 다른 티켓으로 이관되는 번거로움을 겪게 하므로, UX 관점에서 좋지 않습니다.

그렇다면 좋은 UX는 무엇일까? 통상적으로 생각할 때, order guide를 제출해도 일반 Catalog Item을 제출할때와 동일하게 기존 UR 내에서 REQ와 RITM이 생성 및 추적되는 것입니다.

몇 가지 시스템 설정을 통해서 Order Guide의 작동 방식을 일반 Catalog Item과 동일하게 변경시킬 수 있습니다.



## Idea & Architecture

핵심 아이디어는 ServiceNow가 새로운 REQ/RITM과 신규 UR을 생성하여 맵핑하기 전에, 현재 열려있는 UR의 sys_id를 가로채서 새로 생성되는 REQ과 RITM에 강제로 맵핑하는 것입니다.

이 기능을 구현하기 위해 총 4가지 요소를 설정합니다.

- UI Action : 현재 UR의 sys_id를 서버에 넘겨주는 역할

- Script Include : 서버에 있는 값을 조회하기 위한 용도

- Business Rule : 새로운 UR을 만들기 전 가로채는 역할

- Variable set : 맵핑 되어야할 UR의 sys_id를 담아두는 용도




## UI Action

![image](https://github.com/user-attachments/assets/95749b61-9945-4459-9a80-62ea979174d3#.png)

SOW에 있는 버튼인 'Create Request' 버튼을 조금 손봐야한다.


![image](https://github.com/user-attachments/assets/9b6be38b-1df7-43ce-b41e-0ce0b0095ede#.png)

sys_ui_action 테이블 리스트에서 'Create Request' 라는 button을 찾아야한다.

'Create Request'라는 이름으로 2개의 ui action 존재하는데, 하나는 workspace이고 다른 하나는 Core UI를 위한것이다.

우리는 workspace에 있는 것만 먼저 고칠것이니, workspace에 관련된 ui action을 선택해준다.

```javascript
function onClick() {
    var ga = new GlideAjax('sn_uni_req.UniversalRequestAJAXUtils');
    ga.addParam('sysparm_name', 'hasPrimaryTicket');
    ga.addParam('sysparm_ur_sys_id', g_form.getUniqueValue());
    ga.getXMLAnswer(function(answer) {
        if (answer == "true")
            g_form.addErrorMessage(getMessage("You cannot create a new request. The Universal Request already has a primary ticket associated with it."));
        else {
            var saveGa = new GlideAjax('URSessionUtil');
            saveGa.addParam('sysparm_name', 'saveUrId');
            saveGa.addParam('sysparm_ur_id', g_form.getUniqueValue());
            saveGa.getXMLAnswer(function(response) {
                var params = {};
                params.sysparm_parent_table = g_form.getTableName();
                params.sysparm_parent_sys_id = g_form.getUniqueValue();
                g_service_catalog.openCatalogItem('sc_cat_item', '-1', params);
            });
        }
    });
}
```

그 후에 해당 workspace client script를 위와 같이 변경시켜준다.

해당 코드 변경을 통해 server session에 현재 열려있는 UR의 sys_id를 저장할 수 있게 된다.


## Script Include

이제는 그럼 'System Definition > Script Includes' 메뉴로 이동해서 server side script를 작성해보자.

![image](https://github.com/user-attachments/assets/b007cfb6-70b8-417b-90b5-734ed4207b0e#.png)

```javascript
var URSessionUtil = Class.create();
URSessionUtil.prototype = Object.extendsObject(AbstractAjaxProcessor, {
    saveUrId: function() {
        var urId = this.getParameter('sysparm_ur_id');
        gs.getSession().putClientData('source_ur_id', urId);
        return urId;
    },

    getUrId: function() {
        return gs.getSession().getClientData('source_ur_id');
    },

    type: 'URSessionUtil'
});
```

코드에는 2개의 함수가 들어간다.

1. UR sys_id를 session에 저장하는 함수
2. 저장된 sys_id를 꺼내주는 함수


## Business Rule

그렇다면 이제는 REQ가 생성된 이후에 새로운 UR을 생성하지 못하게 그 중간에서 기존 UR 값을 넣어주는 Business Rule을 만들어보자.

![image](https://github.com/user-attachments/assets/cb95c5e7-8ab1-41b1-982e-39781540934d#.png)

Order 번호가 default로 100으로 잡힐 텐데, 기존 system에서 돌아가는 것을 무시하고 먼저 돌리게 하기 위해서 10으로 바꾸주었다.

```javascript
(function executeRule(current, previous /*null when async*/) {
    var existingUrId = gs.getSession().getClientData('source_ur_id');
    
    gs.log("DEBUG: Session UR ID is " + existingUrId, "UR_LINK");

    if (existingUrId) {
        current.universal_request = existingUrId;

        try {
            new sn_uni_req.UniversalRequestUtils().attachPrimaryTicket(existingUrId, current.sys_id, null, true);
        } catch (e) {
            gs.error("UR Link Error: " + e.getMessage());
        }

        gs.getSession().putClientData('source_ur_id', '');
    }
})(current, previous);
```


## Variable Set

Server에 저장해둔 sys_id를 각각 ritm 별로 저장해두고 맵핑시켜야하므로 관리의 편의성을 위해 variable set을 만들어서 적용해야 할 catalog item들에 variable을 추가만 하는 식으로 하자.

![image](https://github.com/user-attachments/assets/6f64093e-e15a-4d57-8e58-0e709f1d79a4#.png)

![image](https://github.com/user-attachments/assets/c32644fc-a90d-4096-8be7-2a9e8f4a82df#.png)

![image](https://github.com/user-attachments/assets/d0f87966-e863-486b-a788-e8553e565aed#.png)

```javascript
function onLoad() {
    var ga = new GlideAjax('URSessionUtil');
    ga.addParam('sysparm_name', 'getUrId');
    ga.getXMLAnswer(function(answer) {
        if (answer) {
            g_form.setValue('parent_sys_id', answer); 
        }
    });
}
```




</div>
