---
layout: post
title: "Service Request Table Structure"
date: 2026-06-02 10:00:00 +0800
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
*The flow will check the table by order numbers. If records has the same order number, flow will check at the same time*

## Table Design

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


## Form

### Type : task

![image](https://github.com/user-attachments/assets/370ff8b4-cbb4-4e99-a0e6-546890457dd6#.png)


### Type : Approve

![image](https://github.com/user-attachments/assets/8a2ddfb3-ce5c-4d65-92e0-d1c77611b29a#.png)


### Type : Event

![image](https://github.com/user-attachments/assets/e92cfdd7-6034-45d0-ae94-195eeeb93409#.png)


### Type : Update RITM

![image](https://github.com/user-attachments/assets/fbf311f2-aaf8-4499-bc2d-b5035e84c550#.png)



## UI Policies

### Set some fields mandatory

![image](https://github.com/user-attachments/assets/628530cf-14ae-467d-988f-327bb360fca5#.png)


### Set Active field default as true

![image](https://github.com/user-attachments/assets/e670d05a-5aa3-4d46-afa3-befeed635725#.png)

![image](https://github.com/user-attachments/assets/8c27024e-8389-4407-afde-e7173efc57f3#.png)




## Client Script

### Shows the Primary Worker field

![image](https://github.com/user-attachments/assets/0c657150-397b-41d0-9d8b-ce2c55a9e0fa#.png)

```javascript
function onChange(control, oldValue, newValue, isLoading, isTemplate) {
	if((g_form.getValue('type') == 'task' || g_form.getValue('type') == 'ritm' )&& newValue != ''){
		g_form.setDisplay('primary_worker', 'true');
	} else {
		g_form.setDisplay('primary_worker', 'false');
	}
   
}
```


### SR AT Line form by type

![image](https://github.com/user-attachments/assets/2d7fe8c8-728e-4fd6-8fa3-729d05b83f45#.png)

```javascript
function onChange(control, oldValue, newValue, isLoading, isTemplate) {
    if (newValue == 'task') {
        g_form.setMandatory('event', false);
        g_form.setDisplay('event', false);
        g_form.setDisplay('groups', true);
        g_form.setDisplay('variables', true);
        g_form.setDisplay('fields', true);
        g_form.setDisplay('business_service', false);
        g_form.setDisplay('service_offering', false);
        g_form.setDisplay('reference_date', false);
        g_form.setDisplay('lead_time', false);
    } else if (newValue == 'approve') {
        g_form.setMandatory('event', false);
        g_form.setDisplay('event', false);
        g_form.setDisplay('groups', true);
        g_form.setDisplay('variables', true);
        g_form.setDisplay('fields', true);
        g_form.setDisplay('business_service', false);
        g_form.setDisplay('service_offering', false);
        g_form.setDisplay('reference_date', false);
        g_form.setDisplay('lead_time', false);
    } else if (newValue == 'event') {
        g_form.setDisplay('event', true);
        g_form.setMandatory('event', true);
        g_form.setDisplay('groups', false);
        g_form.setDisplay('variables', true);
        g_form.setDisplay('fields', true);
        g_form.setDisplay('business_service', false);
        g_form.setDisplay('service_offering', false);
        g_form.setDisplay('reference_date', false);
        g_form.setDisplay('lead_time', false);
    } else {
        g_form.setMandatory('event', false);
        g_form.setDisplay('event', false);
        g_form.setDisplay('groups', true);
        g_form.setDisplay('variables', false);
        g_form.setDisplay('fields', false);
        g_form.setDisplay('business_service', true);
        g_form.setDisplay('service_offering', true);
        g_form.setDisplay('reference_date', true);
        g_form.setDisplay('lead_time', true);
    }
}
```




</div>

<div class="lang-ko" markdown="1">
## Table Design

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




</div>
