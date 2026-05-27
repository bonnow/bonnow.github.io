---
layout: post
title: "How to Search Tickets Without Prefixes in AI Search (Fuzzy Numeric Search)"
date: 2026-05-26 10:00:00 +0800
categories: [servicenow platform]
tags: [ai, search, number, prefix]
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
## 💡 The Problem
When using the ServiceNow portal or AI Search, end-users often find it frustrating that they must type the exact ticket number, including its prefix (e.g., `INC`, `RITM`, `HR`). 

For instance, if a user wants to find ticket `INC0001234` and only types the number `1234`, the search often yields no results.

## 🛠 The Solution: Enabling Fuzzy Numeric Search
This issue can be easily resolved by enabling the **Fuzzy Numeric Search** feature in AI Search. You don't need any complex scripting; by simply adding one System Property, the system will smartly match tickets even if the user drops the prefix or leading zeros.

### Step-by-Step Guide

1. Type `sys_properties.list` in the Application Navigator to navigate to the System Properties table.
2. Search for the property name below. If it doesn't exist, click the **New** button to create it.

Fill in the fields with the following values:

| Field | Value |
| :--- | :--- |
| **Name** | `glide.ais.query.enable_fuzzy_number_match` |
| **Description** | Allow user to search the ticket with the numbers only. |
| **Type** | `true | false` |
| **Value** | `true` |
| **Ignore cache** | `true` (Check the box) |

![System Property Configuration](insert_your_image_path_here.png)
*(Configure as shown in the screenshot above and save.)*

3. Click **Submit** (or **Update**) to save the record.

## 📌 Additional Tips
* **Re-indexing:** If the changes don't reflect immediately in your search results, you may need to re-index the data. Navigate to `AI Search > AI Search Index > Indexed Sources`, locate the relevant table (e.g., Task), and run a **Re-index**.
* **Role Requirements:** You will need the `admin` or `ais_admin` role to create or modify System Properties.

---
**Wrapping Up**
While it might seem like a minor configuration, it drastically improves the search experience and overall UX for end-users. I highly recommend applying this setting before a new project rollout or during a portal enhancement!

</div>

<div class="lang-ko" markdown="1">
## 💡 문제 상황
ServiceNow 포털이나 AI Search를 사용할 때, 현업 사용자들이 종종 불만을 가지는 부분이 있습니다. 바로 티켓을 검색할 때 `INC`, `RITM`, `HR` 같은 Prefix(접두사)를 반드시 포함해서 전체 번호를 입력해야만 검색이 된다는 점입니다. 

예를 들어, 사용자가 `INC0001234` 티켓을 찾기 위해 숫자 `1234`만 입력하면 원하는 결과가 나오지 않아 불편을 겪곤 합니다.

## 🛠 해결 방법: Fuzzy Numeric Search 활성화
이 문제는 AI Search의 **Fuzzy Numeric Search** 기능을 활성화하여 간단하게 해결할 수 있습니다. 별도의 복잡한 스크립트 없이, System Property에 값 하나만 추가해주면 Prefix나 앞자리 '0'을 생략하고 숫자만 입력해도 매칭되는 티켓을 똑똑하게 찾아줍니다.

### 상세 설정 방법

1. 좌측 네비게이터에서 `sys_properties.list`를 입력하여 System Properties 테이블로 이동합니다.
2. 아래 이름으로 된 Property가 있는지 검색해 봅니다. 없다면 **New** 버튼을 눌러 새로 생성합니다.

입력해야 할 값은 다음과 같습니다:

| Field | Value |
| :--- | :--- |
| **Name** | `glide.ais.query.enable_fuzzy_number_match` |
| **Description** | Allow user to search the ticket with the numbers only. (또는 설명 자유롭게 작성) |
| **Type** | `true | false` |
| **Value** | `true` |
| **Ignore cache** | `true` (체크박스 선택) |

![System Property 설정 화면](이미지_경로를_여기에_입력하세요.png)
*(위 설정 화면과 같이 입력 후 저장합니다.)*

3. **Submit** (또는 Update) 버튼을 눌러 저장합니다.

## 📌 추가 팁 (Tip)
* **Re-index 확인:** 설정을 마친 후 즉시 검색에 반영되지 않는다면, AI Search가 해당 데이터를 다시 읽어들일 수 있도록 인덱싱이 필요할 수 있습니다. `AI Search > AI Search Index > Indexed Sources` 메뉴에서 해당하는 테이블(Task 등)을 찾아 **Re-index**를 실행해 주시면 됩니다.
* **접근 권한:** System Property를 수정하려면 `admin` 또는 `ais_admin` 역할(Role)이 필요합니다.

---
**마치며**
사소해 보이는 설정 하나지만, 실제 포털을 사용하는 엔드 유저 입장에서는 검색 경험이 엄청나게 쾌적해집니다. 신규 프로젝트 오픈 전이나 포털 고도화 작업 시 필수로 챙겨두면 좋은 설정이니 꼭 적용해 보시길 추천합니다!




</div>
