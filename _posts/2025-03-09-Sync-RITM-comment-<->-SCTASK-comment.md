---
layout: post
title: "Sync RITM comment ↔ SCTASK comment"
date: 2026-03-09 10:00:00 +0800
categories: [servicenow platform]
tags: [requested item, catalog task, copy, comment, auto]
---

## Introduction
In ServiceNow, keeping communication synchronized between the **Requested Item (RITM)** and its associated **Catalog Tasks (SCTASK)** is essential for a smooth fulfillment process. 

By default, comments made by an end-user on the RITM are not visible to the fulfiller on the Task, and vice versa. This post covers how to automate this synchronization using **Business Rules**.

**KR:** *RITM(요청자)과 SCTASK(실무자) 간의 커뮤니케이션을 원활하게 하기 위해, 한쪽에서 작성된 코멘트가 다른 쪽으로 자동 복사되도록 Business Rule을 설정하는 방법입니다.*
{: .style="color: #888; font-size: 0.9em;"}
---

## 1. RITM comments → SCTASK comments
This configuration ensures that any update provided by the requester on the RITM is instantly shared with the fulfillers working on the SCTASKs.

**KR:** *요청자가 RITM에 남기는 코멘트를 하위의 모든 SCTASK로 자동 복사합니다.*
{: .style="color: #888; font-size: 0.9em;"}

![RITM Configuration](https://github.com/user-attachments/assets/56a75305-5c84-4417-baf6-8f63c5aa36d7)
![RITM Detail](https://github.com/user-attachments/assets/33e584c6-8bf6-41e2-a09e-49e3fff9a956)

### [ Condition ]
To avoid unnecessary updates, we only trigger the rule when the person updating the record is the one for whom the request was made.
```jsx
current.requested_for == gs.getUserID()
```

###  [ Script ]
```jsx
(function executeRule(current, previous /*null when async*/) {
    var chkTask = new GlideRecord('sc_task');
    chkTask.addQuery('request_item', current.getUniqueValue());
    chkTask.query();
    while(chkTask.next()){
        chkTask.comments.setJournalEntry(current.comments);
        chkTask.update();
    }

})(current, previous);
```


## 2. SCTASK comments → RITM comments
When a fulfiller adds a comment to an SCTASK, it needs to be pushed up to the parent RITM so the end-user can see the progress.<br/>
**KR:** *Fulfiller가 SCTASK에 남기는 코멘트를 상위의 RITM로 자동 복사합니다.*{: .style="color: #888; font-size: 0.9em;"}
    
<img width="1379" height="777" alt="Untitled 2" src="https://github.com/user-attachments/assets/a9a2722a-1b66-4166-a9f7-a3fd8bbe0ce8" />

<img width="1370" height="371" alt="Untitled 3" src="https://github.com/user-attachments/assets/f97510b5-7651-4c1b-9de4-d425e4756ed2" />

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
    
    if(ritm.next()) {
        ritm.comments = current.comments;
        ritm.update();
    }

})(current, previous);
```

## Importance Notes
Always ensure your **Conditions** are strictly defined to prevent recursion. Without the "gs.getUserID()" check, the two Business Rules might keep triggering each ohter, creating an infinite loop of comments.<br/>
**KR:** *Warning: 무한 루프 방지를 위해 gs.getUserID()를 확용한 조건 설정이 필수입니다.*{: .style="color: #888; font-size: 0.9em;"}<br
