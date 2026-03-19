---
layout: post
title: "Sync RITM comment ↔ SCTASK comment"
date: 2026-03-09 10:00:00 +0800
categories: [servicenow platform]
tags: [requested item, catalog task, copy, comment, auto]
---

!! Business Rule을 생성해야 합니다.<br/>
!! Need to create Business Rule.

<br/>

적용시키면 end user가 적는 comment가 sctask에도 적히고, 실무자가 적는 comment가 ritm에 적힙니다.<br/>
After configuration, end user's comment (which sits in RITM) will be written in SCTASK, and vice versa.

<br/>

1. RITM comments → SCTASK comments
    
    <img width="1376" height="788" alt="Untitled" src="https://github.com/user-attachments/assets/56a75305-5c84-4417-baf6-8f63c5aa36d7" />

    <img width="1375" height="413" alt="Untitled 1" src="https://github.com/user-attachments/assets/33e584c6-8bf6-41e2-a09e-49e3fff9a956" />
    
    [ Condition ]
    
    ```jsx
    current.requested_for == gs.getUserID()
    ```
    
    [ Script ]
    
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
    

1. SCTASK comments → RITM comments
    
    <img width="1379" height="777" alt="Untitled 2" src="https://github.com/user-attachments/assets/a9a2722a-1b66-4166-a9f7-a3fd8bbe0ce8" />
    
    <img width="1370" height="371" alt="Untitled 3" src="https://github.com/user-attachments/assets/f97510b5-7651-4c1b-9de4-d425e4756ed2" />

    [ Condition ]
    
    ```jsx
    current.request_item.requested_for != gs.getUserID()
    ```
    
    [ Script ]
    
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
