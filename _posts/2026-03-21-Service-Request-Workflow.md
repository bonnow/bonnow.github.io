---
layout: post
title: "Service Request Workflow"
date: 2026-03-21 10:00:00 +0800
categories: [servicenow itsm]
tags: [request, workflow, flow, framework, service, request, framework]
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
Details of the SR workflow.


![image](https://github.com/user-attachments/assets/f8d778ca-e45f-407c-8c6e-f693a384e91a#.png)

## Properties

![image](https://github.com/user-attachments/assets/5197799b-77e2-43f4-a0ad-e80b613c2cc5#.png)

![image](https://github.com/user-attachments/assets/fa087e22-33d4-421b-89ea-ca8dbd93a519#.png)

![image](https://github.com/user-attachments/assets/8be28be2-1781-44e4-944d-cec8920e2b11#.png)

![image](https://github.com/user-attachments/assets/11f6817b-a3bd-40cf-9216-79e90a42cc7c#.png)

![image](https://github.com/user-attachments/assets/07ac4438-6852-407f-87bc-421546ce2b87#.png)

![image](https://github.com/user-attachments/assets/b8e5bc12-2a31-4808-b3cf-fd98beda50ec#.png)

![image](https://github.com/user-attachments/assets/eb2e6c80-1318-4420-aa1d-f4aefc9b74f9#.png)


## 1. Check the SR AT Lines Records

![image](https://github.com/user-attachments/assets/7e75ef71-f3b1-4aed-8e26-b4a43364403c#.png)

```javascript
(function(current, workflow) {

	workflow.scratchpad.srAtLines = [];
	workflow.scratchpad.current = [];
	workflow.scratchpad.now = {
		'catalog_item':'',
		'short_desc':'',
		'desc':'',
		'event':'',
		'fields':'',
		'groups':'',
		'title':'',
		'type':'',
		'variables':'',
		'assign_user':'',
		'assign_group':'',
		'business_service':'',
		'service_offering':'',
		'due_date':'',
		'primary_worker':'',
		'duplicate_approver':''
	};
	workflow.scratchpad.pre_line = [];

	var gr = new GlideRecord('x_hap_sr_flow_sr_at_lines');
	gr.addEncodedQuery('active=true^catalog_item='+current.cat_item.getValue()+'^ORDERBYorder');
	gr.query();

	while(gr.next()){
		workflow.scratchpad.srAtLines.push({
			'sysId' : gr.getUniqueValue(),
			'order' : gr.getValue('order'),
			'type' : gr.getValue('type')
		});
	}
	
})(current, workflow);
```

## 2. Check there are any lines in the same order

![image](https://github.com/user-attachments/assets/8bdf493e-8d44-498b-af68-97b0275b2b29#.png)

```javascript
(function(current, workflow) {
    var first = workflow.scratchpad.srAtLines.shift();
    workflow.scratchpad.current.push(first);

    var sameOrder = 0;

    for (var i = 0; i < workflow.scratchpad.srAtLines.length; i++) {
        if (workflow.scratchpad.srAtLines[i].order == first.order) {
            sameOrder = sameOrder + 1;
        } else {
            break;
        }
    }
    for (var j = sameOrder; j > 0; j--) {
        var tmp = workflow.scratchpad.srAtLines.shift();
        workflow.scratchpad.current.push(tmp);
    }
	
})(current, workflow);
```

## 3. Assgined to field is empty

![image](https://github.com/user-attachments/assets/72652cec-1e43-4c68-b537-ffc9a0a56a0f#.png)


## 4. Set State in WIP

![image](https://github.com/user-attachments/assets/098a56e7-3661-4342-af74-423e2e6d97f9#.png)


## 5. Set State to Open

![image](https://github.com/user-attachments/assets/21b2299c-4695-4a3e-ac65-f7d70d6da84a#.png)


## 6. Check the condition

![image](https://github.com/user-attachments/assets/c57db34b-bafc-4da4-8ff5-b46a89503825#.png)

```javascript
function checkCondition(current, workflow) {
	var ans = 'yes';
	var record = workflow.scratchpad.current[0];

	var gr = new GlideRecord('x_hap_sr_flow_sr_at_lines');
	gr.get(record.sysId);

	var conditionFail = false;
	var scriptedFail = false;

	// 1. Evaluate condition if present
	if (gr.condition) {
		var grRitm = new GlideAggregate('sc_req_item');
		grRitm.addEncodedQuery('sys_id=' + current.getUniqueValue() + '^' + gr.condition);
		grRitm.query();
		if (!grRitm.next()) {
			conditionFail = true;
		}
	}

	// 2. Evaluate scripted_condition if present
	if (gr.scripted_condition) {
		var evaluator = new GlideScopedEvaluator();
		evaluator.putVariable('source', current);
		evaluator.putVariable('answer', null);
		evaluator.evaluateScript(gr, 'scripted_condition', null);
		var result = evaluator.getVariable('answer');

		if (result !== true && result !== null) {
			scriptedFail = true;
		}
	}

	// 3. Final decision
	if (conditionFail || scriptedFail) {
		ans = 'no';
		workflow.scratchpad.current.shift(); // move to next item
	}

	return ans;
}

answer = checkCondition(current, workflow);
```





</div>
<div class="lang-ko" markdown="1">
SR workflow 세부 내용


![image](https://github.com/user-attachments/assets/f8d778ca-e45f-407c-8c6e-f693a384e91a#.png)

## Properties

![image](https://github.com/user-attachments/assets/5197799b-77e2-43f4-a0ad-e80b613c2cc5#.png)

![image](https://github.com/user-attachments/assets/fa087e22-33d4-421b-89ea-ca8dbd93a519#.png)

![image](https://github.com/user-attachments/assets/8be28be2-1781-44e4-944d-cec8920e2b11#.png)

![image](https://github.com/user-attachments/assets/11f6817b-a3bd-40cf-9216-79e90a42cc7c#.png)

![image](https://github.com/user-attachments/assets/07ac4438-6852-407f-87bc-421546ce2b87#.png)

![image](https://github.com/user-attachments/assets/b8e5bc12-2a31-4808-b3cf-fd98beda50ec#.png)

![image](https://github.com/user-attachments/assets/eb2e6c80-1318-4420-aa1d-f4aefc9b74f9#.png)


## 1. Check the SR AT Lines Records

![image](https://github.com/user-attachments/assets/7e75ef71-f3b1-4aed-8e26-b4a43364403c#.png)


</div>
