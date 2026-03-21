---
layout: post
title: "Service Request Framework"
date: 2026-03-21 10:00:00 +0800
categories: [servicenow itsm, korean]
tags: [request, workflow, flow, framework, korean]
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
*It is recommended to manage this flow by using a separate application scope.*

## 1. Intro

When managing ServiceNow, many teams create and maintain a separate flow for each catalog item. However, this approach can cause several problems:

- The number of flows keeps increasing as more catalog item are added.

- Even a small change has to be applied to multiple flows individually.

- It becomes difficult for administrators to understand the overall Service Request flow at a glacnce.



## 2. The Core Idea

Flow-driven operations → Config-driven operations

- Most Request Fulfillment patterns are very similar. The main differences are usually in approvals, task assignment targets, task templates, and conditions.

- From a maintenance perspective, having a separate flow for each item is inefficient. Instead, all items should run through a single workflow.

- Rather than embedding all logic directly in the flow, i wanted to move to a configuraation-driven structure.

- The behavior for each item is controlled by records in a custom table.

- Based on the records in that custom table, actions such as approvals and catalog tasks are processed in sequence according to the defined order.

Instead of creating and maintaining separate flows for each catalog item, i wanted to move the variability into configuration and keep the execution logic standardized.

## 3. Workflow Overview

<img width="1966" height="1365" alt="Screenshot 2026-03-21 at 14 36 23" src="https://github.com/user-attachments/assets/411fa8c2-172c-4d6f-97cc-55bc813b3fba" />


## 4. Custom Table Design

| **Column label** | **Column name** | **Type** | **Reference** | **Max length** | **Attributes** | **Default values** |
| --- | --- | --- | --- | --- | --- | --- |
| Active | active | True/False |  | 40 |  |  |
| Catalog Item | catalog_item | Reference | Catalog Item | 32 | encode_utf8=false |  |
| Condition | condition | Conditions |  | 4000 | condition_builder=v2,edge_encryption_enabled=true,encode_utf8=false |  |
| Created | sys_created_on | Date/Time |  | 40 |  |  |
| Created by | sys_created_by | String |  | 40 |  |  |
| Description | description | String |  | 2000 | edge_encryption_enabled=true |  |
| Event | event | Reference | Event Registration | 32 |  |  |
| Fields | fields | Field List |  | 32 | encode_utf8=false |  |
| Group | groups | List | Group | 32 | encode_utf8=false |  |
| Lead Time | lead_time | Integer |  | 40 |  | 7 |
| Order | order | Integer |  | 40 |  |  |
| Primary Worker | primary_worker | Reference | User | 32 |  |  |
| Reference Date | reference_date | String |  | 200 |  | sys_created_on |
| Scripted Condition | scripted_condition | Script |  | 8000 | edge_encryption_enabled=true |  |
| Service Offering | service_offering | Reference | Offering | 32 | edge_encryption_enabled=true,encode_utf8=false |  |
| Short Description | short_description | String |  | 100 | edge_encryption_enabled=true |  |
| Sys ID | sys_id | Sys ID (GUID) |  | 32 |  |  |
| Table | table | Table Name |  | 32 | encode_utf8=false,base_start=true,edge_encryption_enabled=true | sc_req_item |
| Title | title | String |  | 100 | edge_encryption_enabled=true |  |
| Type | type | Choice |  | 40 | edge_encryption_enabled=true | task
(List : Approve, Task, Event, Update RITM) |
| Updated | sys_updated_on | Date/Time |  | 40 |  |  |
| Updated by | sys_updated_by | String |  | 40 |  |  |
| Updates | sys_mod_count | Integer |  | 40 |  |  |
| Variables | variables | List | Integer | 32 | encode_utf8=false |  |


## 5. Implementation Details
- Workflow
- Table & Form
- System Properties
- Etc

## 6. Sample

1. Catalog Item : ServiceNow Help
    
    This item was created for users who need help related to ServiceNow.
    
    <img width="1919" height="1209" alt="Screenshot 2026-03-21 at 15 02 27" src="https://github.com/user-attachments/assets/3225511c-8856-4bb0-bb5d-e37450557e11" />

    
    1. In the Process Engine, select the worflow named "**SR workflow**".
    2. In the **SR AT Lines** related list, click New and arrange the item's process steps in sequence.

2. Catalog Item : Youtube
    
    This item includes logic so that when a user submits the catalog item through **Now Mobile**, the app is automatically downloaded to the user's phone using integration with Intune.

	<img width="1698" height="1099" alt="Screenshot 2026-03-21 at 15 03 09" src="https://github.com/user-attachments/assets/1a428aa5-cd3a-4bfb-a96d-5def529bbabb" />

	<img width="1072" height="697" alt="Screenshot 2026-03-21 at 15 03 56" src="https://github.com/user-attachments/assets/4cba84f5-2eab-40ef-89d0-76560bc21f38" />

    In this case, tasks are generated, and I specifically created an **Automation Group** and an **Automation User**. When work is assigned to that group or user, it can be used totrigger a flow in Flow Designer.
    

## 7. Precautions

This workflow can handle most item flows, but it cannot cover every case 100%. If you need a more complex flow, i recommend creating a separate flow in Flow Designer.

## 8. Conclusion

The gola of the Serice Request Flow i created is to standardize Request Fulfillment as much as possible and convert it into a structure that is easier to manage and operate.

I hope this flow helps you build a ServiceNow instance that is eaiser and more convenient to manage.

If you have any question about the content, feel free to contact me on LinkedIn. Thank you.

</div>

<div class="lang-kr" markdown="1">
*해당 flow를 구현할때는 별도의 application scope을 활용하여 관리하시는 것을 권장드립니다.*

## 1. Intro

ServiceNow를 관리할때 catalog item의 flow를 item별 개별 flow를 만들어서 관리하는 경우가 많습니다.

허나 해당 방식은 다음과 같은 문제가 생길 수 있습니다.

- Catalog Item마다 개별 Flow를 만들다 보면 Flow 수가 계속 늘어남
- 작은 변경도 여러 Flow를 각각 수정해야 함
- 운영자가 전체 Service Request Flow를 한눈에 보기 어려움

## 2. The Core Idea

Flow-driven 운영 → Config-driven 운영

- Request Fulfillment pattern이 대부분 비슷하다. 차이는 승인, task 생성 대상, task template, 조건 정도가 다르다.
- Item 마다 flow를 따로 갖는 구조가 관리적인 측면에서 비효율적이다. → 모든 flow는 하나의 workflow로만 동작시킨다.
- 로직을 flow에 넣기보다 configuration-driven 구조로 전환하고 싶었다.
- Item별 동작 flow는 custom table의 record로 제어
- Custom table의 record에 따라 approval, catalog task 등의 동작을 order 순서에 따라 처리

Instead of creating and maintaining separate flows for each catalog item, I wanted to move the variability into configuration and keep the execution logic standardized.

## 3. Workflow Overview

<img width="1966" height="1365" alt="Screenshot 2026-03-21 at 14 36 23" src="https://github.com/user-attachments/assets/411fa8c2-172c-4d6f-97cc-55bc813b3fba" />


## 4. Custom Table Design

| **Column label** | **Column name** | **Type** | **Reference** | **Max length** | **Attributes** | **Default values** |
| --- | --- | --- | --- | --- | --- | --- |
| Active | active | True/False |  | 40 |  |  |
| Catalog Item | catalog_item | Reference | Catalog Item | 32 | encode_utf8=false |  |
| Condition | condition | Conditions |  | 4000 | condition_builder=v2,edge_encryption_enabled=true,encode_utf8=false |  |
| Created | sys_created_on | Date/Time |  | 40 |  |  |
| Created by | sys_created_by | String |  | 40 |  |  |
| Description | description | String |  | 2000 | edge_encryption_enabled=true |  |
| Event | event | Reference | Event Registration | 32 |  |  |
| Fields | fields | Field List |  | 32 | encode_utf8=false |  |
| Group | groups | List | Group | 32 | encode_utf8=false |  |
| Lead Time | lead_time | Integer |  | 40 |  | 7 |
| Order | order | Integer |  | 40 |  |  |
| Primary Worker | primary_worker | Reference | User | 32 |  |  |
| Reference Date | reference_date | String |  | 200 |  | sys_created_on |
| Scripted Condition | scripted_condition | Script |  | 8000 | edge_encryption_enabled=true |  |
| Service Offering | service_offering | Reference | Offering | 32 | edge_encryption_enabled=true,encode_utf8=false |  |
| Short Description | short_description | String |  | 100 | edge_encryption_enabled=true |  |
| Sys ID | sys_id | Sys ID (GUID) |  | 32 |  |  |
| Table | table | Table Name |  | 32 | encode_utf8=false,base_start=true,edge_encryption_enabled=true | sc_req_item |
| Title | title | String |  | 100 | edge_encryption_enabled=true |  |
| Type | type | Choice |  | 40 | edge_encryption_enabled=true | task
(List : Approve, Task, Event, Update RITM) |
| Updated | sys_updated_on | Date/Time |  | 40 |  |  |
| Updated by | sys_updated_by | String |  | 40 |  |  |
| Updates | sys_mod_count | Integer |  | 40 |  |  |
| Variables | variables | List | Integer | 32 | encode_utf8=false |  |


## 5. Implementation Details

- Workflow
- Table & Form
- System Properties
- Etc

## 6. Sample

1. Catalog Item : ServiceNow Help
    
    이 item은 사용자들이 ServiceNow 관련해서 도움이 필요한 경우 사용하기 위해 생성되었습니다.
    
    <img width="1919" height="1209" alt="Screenshot 2026-03-21 at 15 02 27" src="https://github.com/user-attachments/assets/3225511c-8856-4bb0-bb5d-e37450557e11" />

    
    1. Process Engine에서 Workflow를 ‘SR workflow’로 선택해줍니다.
    2. SR AT Lines라는 Related List에서 New 버튼을 클릭하여 해당 Item의 흐름을 순서대로 나열해줍니다.

2. Catalog Item : Youtube
    
    이 item은 사용자가 Now Mobile에서 catalog item을 제출하면 intune을 활용하여 자동으로 해당 app을 사용자의 핸드폰에 다운로드 시켜주는 로직을 가지고 있습니다.

	<img width="1698" height="1099" alt="Screenshot 2026-03-21 at 15 03 09" src="https://github.com/user-attachments/assets/1a428aa5-cd3a-4bfb-a96d-5def529bbabb" />

	<img width="1072" height="697" alt="Screenshot 2026-03-21 at 15 03 56" src="https://github.com/user-attachments/assets/4cba84f5-2eab-40ef-89d0-76560bc21f38" />
    
    여기서는 task를 생성하되, 저는 특별히 ‘Automation Group’을 만들고, ‘Automation User’ 라는 유저를 만들어서 해당 그룹이나 유저에게 작업이 할당되면 flow designer에서 flow를 trigger 할 수 있게 하여 활용했습니다.
    

## 7. Precautions

해당 workflow는 대부분의 item flow를 처리할 수 있지만 100% 다 처리 가능하지는 않기에 더욱 복잡한 flow를 구성해야하는 경우에는 flow designer를 활용해서 별도의 flow를 만드는 것을 추천 드립니다.

## 8. Conclusion

제가 만든 Service Request Flow는 가능한 Request Fulfillment를 표준화 가능하고 운영하기 편리한 구조로 바꿔주는 것이 목적입니다.

해당 flow가 여러분의 편한 ServiceNow 환경을 좀 더 관리하고 운영하기 편한 instance를 만드는데 도움이 되기를 바랍니다.

내용 관련해서 궁금하신 점이 있다면 LinkedIn으로 편하게 연락주세요. 감사합니다.


</div>
