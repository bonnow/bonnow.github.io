---
layout: post
title: "Useful Codes"
date: 2026-06-26 10:00:00 +0800
categories: [servicenow platform]
tags: [codes, javascript, server, client]
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
Useful javascript codes during configuring ServiceNow


### Regex for numbers

```javascript
var tnumber = inputs.number;
var regex = /[^0-9]/g;
var result = tnumber.replace(regex, "");
```

### Calculate the Date Difference

```javascript
var dateA = new Date('2023-04-13T17:49:46'.replace('T', ' ').replace(/-/gi, "/") + "");
var dateB = new Date('2023-04-13T17:49:09'replace('T', ' ').replace(/-/gi, "/") + "");
var diffMSec = dateA.getTime() - dateB.getTime();
var diffHour = diffMSec / (60 * 60 * 1000);
```

!! When you put the Date String in ServiceNow. It should be formatted 'YYYY/MM/DD hh:mm:ss'

### Check the date is today

```javascript
var today = new GlideDateTime();

var contracts = new GlideRecord('ast_contract');
contracts.addEncodedQuery('state=active');
contracts.query();

while (contracts.next()) {
    var notiDate = new GlideDateTime(contracts.getValue('notification_date'));

    var ndiffMs = notiDate.getNumericValue() - today.getNumericValue();
    var ndiffDays = parseInt(ndiffMs / (1000 * 60 * 60 * 24));

    if (ndiffDays == 0) {
        gs.eventQueue('contract.notification_date', contracts);
    }
    
}
```

### What's the current language?

```javascript
// result : en, ko
gs.getSession().getLanguage();
```

### Current Instance Name

```javascript
// Instance name
var instanceURL = gs.getProperty('glide.servlet.uri');

// instanceURL -> https://000000.service-now.com/ 

// Instance's table url
gs.getProperty('glide.servlet.uri') + gs.generateURL(strTableName, strSysID)
```

### Currently Logged-In user

```javascript
// server
javascript: gs.getUserID();

// client
g_user.userID
```

### Reference field value [getRefRecord() ]

```javascript
var grUser = current.user.getRefRecord();
if(grUser.isValidRecord() {
	if(grUser.email != current.email_address){
		grUser.email = current.email_address;
		grUser.update();
	}
}
```

### Specific Record URL

```javascript
// (instance 주소) + nav_to.do?uri= + (테이블) + .do?sys_id= + (sys_id)
// 위의 주소 안되는 경우 하단의 주소로 해야함
// (instance 주소) + /now/nav/ui/classic/params/target/ + (테이블) + .do%3Fsys_id%3D + (sys_id)
https://emartdev.service-now.com/nav_to.do?uri=sc_req_item.do?sys_id=${sys_id}


// instance 주소 별로 다르게 동적으로 url 설정하려고 할때
var comm = '@'+gr.request.requested_for.getDisplayValue() + " | ";
if(gs.getProperty('glide.servlet.uri').indexOf('emartdev') == '-1'){
  comm += '[code]<a href="https://eroom.emart.com/workflow/page/form?apmid='+jsn.result.apm.apmId+'&appid='+jsn.result.apm.applicationId+'&pop=1" target="blank">'+gr.number+' 전자결재 링크 </a>[/code]';
} else {
  comm += '[code]<a href="https://eroomdev.emart.com/workflow/page/form?apmid='+jsn.result.apm.apmId+'&appid='+jsn.result.apm.applicationId+'&pop=1" target="blank">'+gr.number+' 전자결재 링크 </a>[/code]';
}
```


### UI Action에서 현재 View 값 가져오기

```javascript
RP.getViewManager().getViewName()

// example
RP.getViewManager().getViewName()!='default'
```


### Client Side에서 preference 값 변경하는 법

```javscript
setPreference(' --이름-- ', '값');

// example
setPreference('com.snc.project_management.home_page.project', g_form.getUniqueValue().toString());
```


### Way to get sys_id

```javascript
// client
g_form.getUniqueValue();

// server
current.getUniqueValue();


### Update 


</div>

<div class="lang-ko" markdown="1">
</div>
