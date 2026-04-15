---
layout: post
title: "How to change user referenced field value at once"
date: 2026-04-15 10:00:00 +0800
categories: [servicenow platform]
tags: [script, user, reference, field, value, change]
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

## Why this script is necessary

**1. Overcoming the Limits of Manual Updates**
In ServiceNow, a user record (sys_user) is heavily referenced across dozens or hundreds of tables—such as 'Caller' on Incidents, 'Assigned to' on Tasks, and 'Owned by' on Configuration Items (CMDB). When an incorrectly created user profile has already been tied to various tickets or assets, manually hunting down and updating every single instance is practically impossible and highly time-consuming. This script solves this by dynamically querying the sys_dictionary to automatically find every field that references the user table.

**2. Ensuring Data Integrity**
If you simply disable or delete the incorrect user record, any records previously linked to it will be left with an orphaned reference (either showing up blank or as a raw Sys ID string). This breaks reporting, dashboards, and data relationships. This script ensures absolute data integrity by seamlessly swapping the incorrect user reference with the correct one across the entire platform, leaving no broken links behind.

**3. Safe and Silent Mass Updates**
The biggest risk when bulk-updating thousands of records is causing system performance degradation or triggering a "Notification Storm." This script safely executes a "silent update." By using setWorkflow(false) and autoSysFields(false), it bypasses all Business Rules, email notifications, and workflows. Furthermore, it preserves the original audit trails by not overwriting the 'Updated' date or 'Updated by' fields on historical records.

---

## Script

Run below script at the "Scripts-Background".

```javascript
// 1. Set the Target Sys IDs
var wrongUserSysId = "(as-is user's sys_id)";
var correctUserSysId = "(to-be user's sys_id)";

// 2. Safety Measure (If true, it only queries and logs, does not perform actual updates)
var isDryRun = false; 

// Find all Dictionary records (fields) that reference the 'sys_user' table
var dictGr = new GlideRecord('sys_dictionary');
dictGr.addQuery('internal_type', 'reference');
dictGr.addQuery('reference', 'sys_user');
// Uncomment the line below if you want to exclude system tables (sys_) or log tables.
// dictGr.addQuery('name', 'NOT LIKE', 'sys_%'); 
dictGr.query();

var totalUpdated = 0;

while (dictGr.next()) {
    var tableName = dictGr.getValue('name');
    var fieldName = dictGr.getValue('element');

    // Skip if the field name is invalid or if it's the 'sys_id' field itself
    if (!fieldName || fieldName == 'sys_id') continue;

    var targetGr = new GlideRecord(tableName);

    // Check if the table is actually valid (Prevents errors from ghost fields existing only in the dictionary)
    if (!targetGr.isValid()) continue;

    targetGr.addQuery(fieldName, wrongUserSysId);
    targetGr.query();

    var rowCount = targetGr.getRowCount();
    
    if (rowCount > 0) {
        gs.print('[' + tableName + '] table, [' + fieldName + '] field: Found ' + rowCount + ' records');

        if (!isDryRun) {
            while (targetGr.next()) {
                targetGr.setValue(fieldName, correctUserSysId);

                // [IMPORTANT SAFETY MEASURES]
                // 1. Prevent massive triggering of Business Rules, Email Notifications, and Workflows
                targetGr.setWorkflow(false); 
                
                // 2. Do not overwrite 'sys_updated_on' and 'sys_updated_by' with the current time and script executor
                targetGr.autoSysFields(false);

                targetGr.update();
            }
            totalUpdated += rowCount;
        }
    }
}

if (isDryRun) {
    gs.print('--- 🛑 DRY RUN COMPLETE: No actual data was changed. Please review the pending changes. ---');
} else {
    gs.print('--- ✅ EXECUTION COMPLETE: A total of ' + totalUpdated + ' records have been updated. ---');
}
```

</div>

<div class="lang-ko" markdown="1">

## 이 스크립트가 필요한 이유 (Why this script is necessary)

**1. 수작업의 한계 및 시간 단축 (Overcoming Manual Limitations)**
ServiceNow에서 사용자 레코드(sys_user)는 Incident의 'Caller', Task의 'Assigned to', CMDB의 'Owned by' 등 수십에서 수백 개의 테이블에서 광범위하게 참조됩니다. 잘못 생성된 사용자 계정이 이미 여러 티켓이나 데이터에 연결되어 버린 경우, 이를 사람이 일일이 찾아내어 수정하는 것은 사실상 불가능하며 엄청난 시간이 소모됩니다. 이 스크립트는 시스템의 데이터 사전(sys_dictionary)을 동적으로 훑어 수정이 필요한 모든 곳을 자동으로 찾아냅니다.

**2. 데이터 무결성 확보 (Ensuring Data Integrity)**
잘못된 사용자 레코드를 단순히 비활성화하거나 삭제해 버리면, 기존에 연결되어 있던 데이터들은 대상이 없는 '고아(Orphan)' 참조 값(빈 값 또는 Sys ID 텍스트)을 가지게 되어 리포트나 대시보드 통계에 치명적인 오류를 일으킵니다. 이 스크립트는 잘못된 참조 값을 올바른 사용자로 일괄 치환하여 데이터의 무결성과 연결성을 완벽하게 유지합니다.

**3. 안전한 대량 업데이트 (Safe & Silent Mass Updates)**
수천 건의 데이터를 한 번에 업데이트할 때 가장 위험한 것은 시스템 부하와 'Notification Storm(알림 폭탄)'입니다. 이 스크립트는 setWorkflow(false)와 autoSysFields(false) 함수를 사용하여 비즈니스 룰, 이메일 알림 발송, 워크플로우를 강제로 차단합니다. 또한 기존 티켓의 '수정일(Updated)'이나 '수정자(Updated by)' 정보를 덮어쓰지 않아 감사(Audit) 기록의 훼손 없이 조용하고 안전하게 데이터만 교정합니다.

---

## Script

아래의 스크립트를 "Scripts-Background"에서 실행하면 됩니다.


```javascript
// 1. 변경 대상 Sys ID 설정
var wrongUserSysId = '(as-is user's sys_id)';
var correctUserSysId = '(to-be user's sys_id)';

// 2. 안전 장치 (true일 경우 조회만 하고 업데이트는 하지 않음)
var isDryRun = false; 

// sys_user를 Reference로 사용하는 모든 Dictionary(필드) 검색
var dictGr = new GlideRecord('sys_dictionary');
dictGr.addQuery('internal_type', 'reference');
dictGr.addQuery('reference', 'sys_user');
// 시스템 테이블(sys_)이나 로그 기록성 테이블을 제외하고 싶다면 아래 주석을 해제하세요.
// dictGr.addQuery('name', 'NOT LIKE', 'sys_%'); 
dictGr.query();

var totalUpdated = 0;

while (dictGr.next()) {
    var tableName = dictGr.getValue('name');
    var fieldName = dictGr.getValue('element');

    // sys_id 필드 자체이거나 유효하지 않은 필드명은 건너뜀
    if (!fieldName || fieldName == 'sys_id') continue;

    var targetGr = new GlideRecord(tableName);

    // 테이블이 실제로 유효한지 확인 (Dictionary에만 존재하는 고스트 필드 에러 방지)
    if (!targetGr.isValid()) continue;

    targetGr.addQuery(fieldName, wrongUserSysId);
    targetGr.query();

    var rowCount = targetGr.getRowCount();
    
    if (rowCount > 0) {
        gs.print('[' + tableName + '] 테이블의 [' + fieldName + '] 필드에서 ' + rowCount + '건 발견');

        if (!isDryRun) {
            while (targetGr.next()) {
                targetGr.setValue(fieldName, correctUserSysId);

                // [중요 안전장치]
                // 1. 비즈니스 룰, 이메일 알림, 워크플로우가 대량으로 트리거되는 것을 방지
                targetGr.setWorkflow(false); 
                
                // 2. sys_updated_on, sys_updated_by 값을 스크립트 실행자와 현재 시간으로 덮어쓰지 않음
                targetGr.autoSysFields(false);

                targetGr.update();
            }
            totalUpdated += rowCount;
        }
    }
}

if (isDryRun) {
    gs.print('--- 🛑 DRY RUN 완료: 실제 데이터는 변경되지 않았습니다. 변경될 내역을 확인하세요. ---');
} else {
    gs.print('--- ✅ 실행 완료: 총 ' + totalUpdated + '건의 레코드가 업데이트되었습니다. ---');
}
```
</div>
